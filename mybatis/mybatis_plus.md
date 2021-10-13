

# SpringBoot集成Mybatis-Plus框架



<br/>



MyBatis-Plus（简称 MP）是一个MyBatis的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。



<br/>

具体可以参考MyBatis-Plus官网：[MyBatis-Plus官网](https://mp.baomidou.com)



<br/>



[TOC]

<br/>



## 环境



<br/>



- Java1.8
- maven
- spring boot 2.2.4.RELEASE
- mysql



<br/>





maven xml 配置文件

~~~xml

<!-- spring boot配置-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.4.RELEASE</version>
</parent>



<!-- 
	mybatis-plus（包含了mybatis的包）
	引入了此依赖后，不需要在引入mybatis的其他包
-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.3.2</version>
</dependency>
		

<!-- MySql驱动 -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
</dependency>

~~~



<br/>



## 数据库表



<br/>



用户基本信息表

~~~sql

-- 建表语句

CREATE TABLE `user_info` (
  `user_id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '用户ID主键',
  `user_name` varchar(100) NOT NULL COMMENT '用户姓名',
  `user_idcard` varchar(20) DEFAULT NULL COMMENT '身份证',
  `age` int(5) DEFAULT NULL COMMENT '年龄',
  `home_address` varchar(500) DEFAULT NULL COMMENT '家庭地址',
  `height` int(5) DEFAULT NULL COMMENT '身高',
  `birth_day` date DEFAULT NULL COMMENT '出生日期',
  `sex` varchar(5) DEFAULT NULL COMMENT '性别',
  PRIMARY KEY (`user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ;

~~~



<br/>





## Java实体类 UserInfo



<br/>



~~~java

/**
 * @description
 *	用户基本信息
 * @author TianwYam
 * @date 2021年10月13日下午4:24:36
 */
@Data
@Builder
@TableName("user_info")
public class UserInfo {
	
	// 用户ID
    // @TableId 标注为表主键ID，若是没有标注，则 updateById会报错
	@TableId
	private long userId ;
	
	// 用户姓名
	private String userName ;
	
	// 用户身份证
	private String userIdcard ;
	
	// 家庭地址
	private String homeAddress ;
	
	// 年龄
	private int age ;
	
	// 身高CM
	private int height ;
	
	// 性别
	private String sex ;
	
	// 出生日期
	private Date birthDay ;

}

~~~

<br/>

注意：@TableId 标注为表主键ID，若是没有标注，则 updateById会报错，具体报错，参考下面的：CRUD操作/更新





<br/>



## application.yml 主配置



<br/>



配置数据源

~~~yaml
server:
  port: 8080
  servlet:
    context-path: /
    
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: mysql

~~~



<br/>



## 编写mapper



<br/>



集成mybatis-plus mapper接口继承 BaseMapper 即可，然后对单表的CRUD的操作就可以不用编写xml文件了，类似JPA框架，非常方便

~~~java

@Repository
public interface IUserInfoMapper extends BaseMapper<UserInfo>{

}

~~~



<br/>



BaseMapper 对单表的常用方法：

![BaseMapper.java](img\mybatis_plus_basemapper.png)



<br/>



## 主启动类



<br/>

~~~java

@SpringBootApplication
@MapperScan("com.tianya.springboot.mybatis.plus.mapper")
public class LearnMybatisPlusApplication {
	
	public static void main(String[] args) {
		SpringApplication.run(LearnMybatisPlusApplication.class, args);
	}

}

~~~



<br/>



## CRUD操作



<br/>



来实际测试一下 mybatis-plus便捷之处

此处使用了一个自动随机生成测试数据的工具jar

~~~xml

<!-- 生成测试数据工具 -->
<dependency>
	<groupId>com.github.javafaker</groupId>
	<artifactId>javafaker</artifactId>
	<version>1.0.2</version>
</dependency>

~~~

这个工具可以随机生成测试数据，避免自己制造测试数据头痛



<br/>



### 新增



<br/>



~~~java

// 插入对象
int insert(T entity);

~~~



<br/>



测试新增用户基本信息：

~~~java

@Test
public void insert() {
	
	Faker faker = new Faker(Locale.SIMPLIFIED_CHINESE);
	
	// 测试 新增 10 个用户
	for (int i = 0; i < 10; i++) {
		
		// 用户信息
		UserInfo userInfo = UserInfo.builder()
				.userName(faker.name().fullName())
				.userIdcard(faker.idNumber().invalid())
				.homeAddress(faker.address().fullAddress())
				.birthDay(faker.date().birthday())
				.age(faker.number().numberBetween(1, 120))
				.height(faker.number().numberBetween(50, 200))
				.sex(SEX_MAP.get(faker.number().numberBetween(0, 2)))
				.build();
        
        // 新增
		userInfoMapper.insert(userInfo);
	}
	
}

~~~



结果如下：

~~~json
[
	{
		"age":75,
		"birthDay":"1974年10月28日",
		"height":94,
		"homeAddress":"余巷",
		"sex":"女",
		"userId":11,
		"userIdcard":"000-98-3709",
		"userName":"林烨霖"
	},
	{
		"age":87,
		"birthDay":"1977年09月05日",
		"height":118,
		"homeAddress":"Suite 078 袁桥36104号, 金华, 浙 723135",
		"sex":"女",
		"userId":27,
		"userIdcard":"114-00-3888",
		"userName":"邹雨泽"
	},
	{
		"age":12,
		"birthDay":"1965年09月05日",
		"height":150,
		"homeAddress":"Suite 118 谢中心0798号, 衢州, 桂 350938",
		"sex":"男",
		"userId":28,
		"userIdcard":"435-25-0000",
		"userName":"范金鑫"
	},
	{
		"age":30,
		"birthDay":"1996年07月04日",
		"height":84,
		"homeAddress":"阎中心50号, 海口, 湘 888025",
		"sex":"女",
		"userId":29,
		"userIdcard":"162-11-0000",
		"userName":"程炫明"
	},
	{
		"age":84,
		"birthDay":"1972年06月14日",
		"height":144,
		"homeAddress":"刘街3928号, 平顶山, 豫 904305",
		"sex":"男",
		"userId":30,
		"userIdcard":"638-77-0000",
		"userName":"汪鑫鹏"
	}
]
~~~



<br/>



### 查询



<br/>



~~~java
// 根据 entity 条件，查询全部记录 封装 实体对象
List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据 Wrapper 条件，查询全部记录
List<Map<String, Object>> selectMaps(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据 Wrapper 条件 查询总记录数
Integer selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 查询单条记录
T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据主键ID查询
T selectById(Serializable id);
~~~



<br/>





查询所有

~~~java
@Test
public void getList() {
	// 查询所有
	List<UserInfo> selectList = userInfoMapper.selectList(null);
	System.out.println("所有的用户基本信息：");
	System.out.println(JSON.toJSONString(selectList, true));
}
~~~



<br/>







### 删除



<br/>



~~~java

// 根据ID删除
int deleteById(Serializable id);

// 根据 columnMap 条件，删除记录
int deleteByMap(Map<String, Object> columnMap);

// 根据 entity 条件，删除记录
int delete(Wrapper<T> wrapper);

// 删除（根据ID 批量删除）
int deleteBatchIds(Collection<? extends Serializable> idList);

~~~



<br/>



根据ID删除

~~~java
@Test
public void delete() {
	
	// 删除
	userInfoMapper.deleteById(20);
	
}

~~~



<br/>



### 更新



<br/>



~~~java

// 根据 ID 修改
int updateById(@Param(Constants.ENTITY) T entity);

// 根据 whereEntity 条件，更新记录
int update( T entity,  Wrapper<T> updateWrapper);

~~~



<br/>



更加ID进行修改

~~~java

@Test
public void update() {
	
	// 查询单个
	UserInfo userInfo = userInfoMapper.selectOne(Wrappers.lambdaQuery(UserInfo.class)
			.eq(UserInfo::getUserId, 27));
	
	System.out.println("修改前：");
	System.out.println(JSON.toJSONString(userInfo, true));
	
	// 修改值
	userInfo.setAge(70);
	userInfo.setSex(SEX_MAP.get(1));
	userInfo.setHeight(178);
	
	// 更新
	userInfoMapper.updateById(userInfo);
	
	System.out.println("修改后：");
	System.out.println(JSON.toJSONString(userInfo, true));
}

~~~

<br/>

若是出现以下错误，则说明 实体类没有添加 表ID主键注解：@TableId

<br/>



~~~verilog
org.mybatis.spring.MyBatisSystemException: nested exception is org.apache.ibatis.reflection.ReflectionException: There is no getter for property named 'null' in 'class com.tianya.springboot.mybatis.plus.entity.UserInfo'
	at org.mybatis.spring.MyBatisExceptionTranslator.translateExceptionIfPossible(MyBatisExceptionTranslator.java:92)
	at org.mybatis.spring.SqlSessionTemplate$SqlSessionInterceptor.invoke(SqlSessionTemplate.java:440)
	at com.sun.proxy.$Proxy83.update(Unknown Source)
	at org.mybatis.spring.SqlSessionTemplate.update(SqlSessionTemplate.java:287)
	at com.baomidou.mybatisplus.core.override.MybatisMapperMethod.execute(MybatisMapperMethod.java:65)
	at com.baomidou.mybatisplus.core.override.MybatisMapperProxy.invoke(MybatisMapperProxy.java:96)
	at com.sun.proxy.$Proxy104.updateById(Unknown Source)
	
	... 82 more


~~~



<br/>



结果：

~~~json
## 修改前：
{
	"age":67,
	"birthDay":"1977年09月05日",
	"height":118,
	"homeAddress":"Suite 078 袁桥36104号, 金华, 浙 723135",
	"sex":"女",
	"userId":27,
	"userIdcard":"114-00-3888",
	"userName":"邹雨泽"
}

## 修改后：
{
	"age":70,
	"birthDay":"1977年09月05日",
	"height":178,
	"homeAddress":"Suite 078 袁桥36104号, 金华, 浙 723135",
	"sex":"男",
	"userId":27,
	"userIdcard":"114-00-3888",
	"userName":"邹雨泽"
}
~~~





<br/>

