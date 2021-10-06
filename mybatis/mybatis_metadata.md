# mybatis 中对元数据的操作



<br>



[TOC]



<br>



背景：在目前流行的持久层框架mybatis中，有些场景下需要获取数据库表的元数据(字段名称、字段类型、字段长度、字段注释等)操作，可以实现让繁琐的事情简单化，自动生成Javabean或者mybatis中的mapper.xml文件等等。mybatis框架的逆向工程（mybatis-generate）也可以实现部分功能，有些场景是需要在Java运行时去动态获取字段列表等。



<br>

在数据库 test 下建一张表 user

建表：

~~~sql
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `username` varchar(255) DEFAULT NULL COMMENT '用户姓名',
  `password` varchar(255) DEFAULT NULL COMMENT '用户密码',
  `isadmin` int(1) DEFAULT NULL COMMENT '是否是管理员',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
~~~

字段：

![数据库test下的user表字段](img\table_test_user.jpg)



<br>



## 1. 使用MySQL内部数据库information_schema表查询实现



<br>



通过查询试图，读取表的字段等信息



<br>



### 1.1 MySQL内部字段表 information_schema.COLUMNS



<br>



MySQL中，存放表字段的内部表是：information_schema.COLUMNS

![information_schema.COLUMNS](img\metadata_columns.jpg)



<br>



### 1.2 mybatis的mapper.xml文件



<br>

<br>



例如：查询数据库 test 下的 表 user 的 表字段列名等信息，在mapper.xml文件中

~~~xml
   
<select id="getUserTableColumns" resultType="map">
    SELECT 
    	* 
    FROM 
    	information_schema.COLUMNS 
    WHERE 
    	TABLE_SCHEMA = 'test' 
    AND 
    	TABLE_NAME = 'user'
</select>
    
~~~



<br>



TABLE_SCHEMA：数据库名称

TABLE_NAME：表名



<br>



mapper.java

~~~java
@Mapper
public interface IUserMapper {
	
	public List<Map<String, Object>> getUserTableColumns();

}
~~~



<br>





### 1.3 mybatis服务层



<br>



~~~java

/**
* @description
*	获取 元 数据
* @author TianwYam
* @date 2021年10月6日上午9:05:47
* @return
*/
public List<Map<String, Object>> getUserTableColumns(){
	return userMapper.getUserTableColumns();
}
~~~



<br>





### 1.4 输出结果



~~~json
[
	{
		"TABLE_CATALOG":"def",
		"IS_NULLABLE":"NO",
		"TABLE_NAME":"user",
		"TABLE_SCHEMA":"test",
		"EXTRA":"auto_increment",
		"COLUMN_NAME":"id",
		"COLUMN_KEY":"PRI",
		"NUMERIC_PRECISION":10,
		"PRIVILEGES":"select,insert,update,references",
		"COLUMN_COMMENT":"主键ID",
		"NUMERIC_SCALE":0,
		"COLUMN_TYPE":"int(11)",
		"ORDINAL_POSITION":1,
		"DATA_TYPE":"int"
	},
	{
		"TABLE_CATALOG":"def",
		"IS_NULLABLE":"YES",
		"TABLE_NAME":"user",
		"TABLE_SCHEMA":"test",
		"EXTRA":"",
		"COLUMN_NAME":"username",
		"COLUMN_KEY":"",
		"CHARACTER_OCTET_LENGTH":765,
		"PRIVILEGES":"select,insert,update,references",
		"COLUMN_COMMENT":"用户姓名",
		"COLLATION_NAME":"utf8_general_ci",
		"COLUMN_TYPE":"varchar(255)",
		"ORDINAL_POSITION":2,
		"CHARACTER_MAXIMUM_LENGTH":255,
		"DATA_TYPE":"varchar",
		"CHARACTER_SET_NAME":"utf8"
	},
	{
		"TABLE_CATALOG":"def",
		"IS_NULLABLE":"YES",
		"TABLE_NAME":"user",
		"TABLE_SCHEMA":"test",
		"EXTRA":"",
		"COLUMN_NAME":"password",
		"COLUMN_KEY":"",
		"CHARACTER_OCTET_LENGTH":765,
		"PRIVILEGES":"select,insert,update,references",
		"COLUMN_COMMENT":"用户密码",
		"COLLATION_NAME":"utf8_general_ci",
		"COLUMN_TYPE":"varchar(255)",
		"ORDINAL_POSITION":3,
		"CHARACTER_MAXIMUM_LENGTH":255,
		"DATA_TYPE":"varchar",
		"CHARACTER_SET_NAME":"utf8"
	},
	{
		"TABLE_CATALOG":"def",
		"IS_NULLABLE":"YES",
		"TABLE_NAME":"user",
		"TABLE_SCHEMA":"test",
		"EXTRA":"",
		"COLUMN_NAME":"isadmin",
		"COLUMN_KEY":"",
		"NUMERIC_PRECISION":10,
		"PRIVILEGES":"select,insert,update,references",
		"COLUMN_COMMENT":"是否是管理员",
		"NUMERIC_SCALE":0,
		"COLUMN_TYPE":"int(1)",
		"ORDINAL_POSITION":4,
		"DATA_TYPE":"int"
	}
]
~~~





<br>



## 2. 使用jdbc方式获取元数据



<br>



### 2.1 使用原生jdbc获取元数据



<br>



#### 2.1.1 原生JDBC



<br>



~~~java

/**
 * @description
 *	JDBC 获取元数据方式
 * @author TianwYam
 * @date 2021年10月6日上午10:39:46
 */
public class JdbcTest {
	
	
	public static void main(String[] args) throws Exception {
		
		// 1. 配置 数据库链接信息
		String driver="com.mysql.cj.jdbc.Driver";
		String url="jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC";
		String username="root";
		String password="mysql";

		//2. 加载驱动
		Class.forName(driver);

		//3. 建立连接
		Connection connection= DriverManager.getConnection(url,username,password);
		
		//4. 获取元数据
		DatabaseMetaData metaData = connection.getMetaData();
		
		//5. 获取表的字段 列
		ResultSet resultSet = metaData.getColumns("test", null, "user", null);

		//6. 处理ResultSet
		while(resultSet.next()){
		    //处理resultSet
		}

		//7. 释放资源
	    resultSet.close();
	    connection.close();
	}

}
~~~



 这儿是采用原生jdbc方式对元数据操作获取表的字段所有列，基本上都是以上几步



<br>



#### 2.1.2 DatabaseMetaData.getColumns



<br>



~~~java
// 获取表所有字段
DatabaseMetaData.getColumns(String catalog, String schemaPattern, String tableNamePattern, String columnNamePattern) ;

// catalog 分类 -- 数据库名称
// schemaPattern 约束
// tableNamePattern 表名
// columnNamePattern 字段列名
~~~



<br>

getColumns 方法返回值有：

![](img\getColumns.jpg)





<br>





### 2.2 mybatis采用JDBC方式获取元数据



<br>



持久层框架不同，需要对第三步中的获取连接 connection有所改变

<br>



思路：Spring boot集成mybatis框架中，获取connection是从 SqlSessionFactory 中获取到 SqlSession，然后从 SqlSession 中获取到 Connection



<br>



#### 2.2.1 mybatis服务层



<br>



新建类 封装表字段返回数据结构

~~~java

/**
 * @description
 *	表 元数据 字段列信息
 * @author TianwYam
 * @date 2021年10月5日下午8:08:08
 */
@Data
@Builder
public class TableColumnBean {
	
	private String dbName ;
	
	private String tableName ;
	
	private String columnName ;
	
	private String autoIncrement ;
	
	private String generatedColumn  ;
	
	private String columnType ;
	
	private int columnLength ;
	
	private String columnRemark ;

}
~~~



<br>



service类

~~~java

@Service
public class UserService {
	
    // 自动引入(前提是 搭建了spring boot+mybatis架构)
	@Autowired
	private SqlSessionFactory sqlSessionFactory ;
	
	
	/**
	 * @description
	 *	获取 user 表的 元数据 表结构
	 * @author TianwYam
	 * @date 2021年10月6日上午9:05:14
	 * @return
	 */
	public List<TableColumnBean> getUserMeta() {
		
		List<TableColumnBean> columnList = new ArrayList<>();
		try {
			// 获取连接、获取元数据
			DatabaseMetaData metaData = sqlSessionFactory.openSession().getConnection().getMetaData();
			ResultSet columns = metaData.getColumns("test", null, "user", null);
			
            // 对结果集 ResultSet 操作
			System.out.println("column: ");
			while(columns.next()){
				TableColumnBean columnBean = TableColumnBean.builder()
						.dbName(columns.getString("TABLE_CAT"))
						.tableName(columns.getString("TABLE_NAME"))
						.columnName(columns.getString("COLUMN_NAME"))
						.autoIncrement(columns.getString("IS_AUTOINCREMENT"))
						.generatedColumn(columns.getString("IS_GENERATEDCOLUMN"))
						.columnType(columns.getString("TYPE_NAME"))
						.columnLength(columns.getInt("COLUMN_SIZE"))
						.columnRemark(columns.getString("REMARKS"))
						.build();
				columnList.add(columnBean);
	        }
			
			System.out.println(JSON.toJSONString(columnList, true));
		
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		return columnList;
	}	

}

~~~



<br>



#### 2.2.2 结果



~~~json
[
	{
		"autoIncrement":"YES",
		"columnLength":10,
		"columnName":"id",
		"columnRemark":"主键ID",
		"columnType":"INT",
		"dbName":"test",
		"generatedColumn":"NO",
		"tableName":"user"
	},
	{
		"autoIncrement":"NO",
		"columnLength":255,
		"columnName":"username",
		"columnRemark":"用户姓名",
		"columnType":"VARCHAR",
		"dbName":"test",
		"generatedColumn":"NO",
		"tableName":"user"
	},
	{
		"autoIncrement":"NO",
		"columnLength":255,
		"columnName":"password",
		"columnRemark":"用户密码",
		"columnType":"VARCHAR",
		"dbName":"test",
		"generatedColumn":"NO",
		"tableName":"user"
	},
	{
		"autoIncrement":"NO",
		"columnLength":10,
		"columnName":"isadmin",
		"columnRemark":"是否是管理员",
		"columnType":"INT",
		"dbName":"test",
		"generatedColumn":"NO",
		"tableName":"user"
	}
]
~~~



最核心的就是：

~~~java
DatabaseMetaData metaData = sqlSessionFactory.openSession().getConnection().getMetaData();
~~~





