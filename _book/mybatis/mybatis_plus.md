

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
- mybatis-plus-boot-starte 3.3.2
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
    @TableField
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
    // JSON 输出格式化
    @JSONField(format = "yyyy年MM月dd日")
	private Date birthDay ;

}

~~~

<br/>

注意：@TableId 标注为表主键ID，若是没有标注，则 updateById会报错，具体报错，参考下面的：CRUD操作/更新

<br/>

其他字段若是跟数据库保持一致或者数据库是以下划线分割，Java类属性是以驼峰命名的，则可以不用添加 @TableField，mybatis-plus框架会自动匹配

<br/>

数据库字段值,  不需要配置该值的情况:  

当 `com.baomidou.mybatisplus.core.MybatisConfiguration.mapUnderscoreToCamelCase` 为 true 时, (mp下默认是true,mybatis默认是false), 数据库字段值.replace("_","").toUpperCase() == 实体属性名.toUpperCase() 

当 `com.baomidou.mybatisplus.core.MybatisConfiguration.mapUnderscoreToCamelCase` 为 false 时,  数据库字段值.toUpperCase() == 实体属性名.toUpperCase()

<br/>

若是JavaBean中某些属性不是数据库表字段的，则可以添加 @TableField(exist = false) 来避免自动生成的SQL字段不存在问题





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



## Mapper CRUD操作



<br/>



mapper接口 继承 BaseMapper 接口，就可以实现对单表的 CRUD快捷API操作



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

<br/>

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



### 分页查询



<br/>



分页查询 mapper中 对应的 API 操作是：

~~~java

// 根据 entity 条件，查询全部记录（并翻页）
<E extends IPage<T>> E selectPage(E page, Wrapper<T> queryWrapper);

// 根据 Wrapper 条件，查询全部记录（并翻页）
<E extends IPage<Map<String, Object>>> E selectMapsPage(E page, Wrapper<T> queryWrapper);
~~~

<br/>

但是此 API操作，需要搭配 mybatis-plus中的分页插件才有效果，否则查询的 全部，没有分页限制

<br/>



#### 分页插件配置

<br/>

~~~java

/**
 * @description
 *	Mybatis-Plus总配置类 分页插件
 * @author TianwYam
 * @date 2021年10月14日上午11:16:45
 */
@Configuration
public class MybatisPlusConfig {
	
	@Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }

}
~~~

<br/>



配置完分页插件后，可以使用mybatis-plus提供的分页API进行分页查询，也可以自定义XML文件编写查询语句



<br/>



#### 分页查询 测试结果

<br/>

~~~java
@Test
public void getQueryList() {
	
	// 查询条件（查询身高在1米-2米之间的 以年龄倒序排序，查询第一页3个 TOP3）
	LambdaQueryWrapper<UserInfo> queryWrapper = Wrappers.lambdaQuery(UserInfo.class)
			.orderByDesc(UserInfo::getAge)
			.between(UserInfo::getHeight, 100, 200);
	
	Page<UserInfo> page = new Page<UserInfo>(1, 3);
	page.setSearchCount(true);
	
	// 若是没有配置 分页插件，则 selectPage 分页API 不生效
	page = userInfoMapper.selectPage(page, queryWrapper);
	
	System.out.println("分页查询：");
	System.out.println("总记录数：" + page.getTotal());
	System.out.println(JSON.toJSONString(page, true));
}
~~~

<br/>

结果如下：

~~~json
{
	"current":1,
	"hitCount":false,
	"optimizeCountSql":true,
	"orders":[],
	"pages":5,
	"records":[
		{
			"age":93,
			"birthDay":"1970年07月23日",
			"height":157,
			"homeAddress":"段路7号, 北京, 宁 451930",
			"sex":"男",
			"userId":25,
			"userIdcard":"666-37-8505",
			"userName":"朱鹤轩"
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
		},
		{
			"age":79,
			"birthDay":"1984年01月17日",
			"height":113,
			"homeAddress":"赵街",
			"sex":"男",
			"userId":14,
			"userIdcard":"666-95-6869",
			"userName":"阎俊驰"
		}
	],
	"searchCount":true,
	"size":3,
	"total":14
}
~~~









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





## Service CRUD操作



<br/>

mybatis-plus框架 提供了 针对 服务层service的一个基类接口，提供更加丰富的 CRUD API 操作

~~~java
com.baomidou.mybatisplus.extension.service.IService<T>
~~~

<br/>

只需要 实现 IService 即可

<br/>

提供的一系列操作API：

~~~java


    /**
     * 默认批次提交数量
     */
    int DEFAULT_BATCH_SIZE = 1000;

    /**
     * 插入一条记录（选择字段，策略插入）
     *
     * @param entity 实体对象
     */
    default boolean save(T entity) {
        return SqlHelper.retBool(getBaseMapper().insert(entity));
    }

    /**
     * 插入（批量）
     *
     * @param entityList 实体对象集合
     */
    @Transactional(rollbackFor = Exception.class)
    default boolean saveBatch(Collection<T> entityList) {
        return saveBatch(entityList, DEFAULT_BATCH_SIZE);
    }

    /**
     * 插入（批量）
     *
     * @param entityList 实体对象集合
     * @param batchSize  插入批次数量
     */
    boolean saveBatch(Collection<T> entityList, int batchSize);

    /**
     * 批量修改插入
     *
     * @param entityList 实体对象集合
     */
    @Transactional(rollbackFor = Exception.class)
    default boolean saveOrUpdateBatch(Collection<T> entityList) {
        return saveOrUpdateBatch(entityList, DEFAULT_BATCH_SIZE);
    }

    /**
     * 批量修改插入
     *
     * @param entityList 实体对象集合
     * @param batchSize  每次的数量
     */
    boolean saveOrUpdateBatch(Collection<T> entityList, int batchSize);

    /**
     * 根据 ID 删除
     *
     * @param id 主键ID
     */
    default boolean removeById(Serializable id) {
        return SqlHelper.retBool(getBaseMapper().deleteById(id));
    }

    /**
     * 根据 columnMap 条件，删除记录
     *
     * @param columnMap 表字段 map 对象
     */
    default boolean removeByMap(Map<String, Object> columnMap) {
        Assert.notEmpty(columnMap, "error: columnMap must not be empty");
        return SqlHelper.retBool(getBaseMapper().deleteByMap(columnMap));
    }

    /**
     * 根据 entity 条件，删除记录
     *
     * @param queryWrapper 实体包装类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    default boolean remove(Wrapper<T> queryWrapper) {
        return SqlHelper.retBool(getBaseMapper().delete(queryWrapper));
    }

    /**
     * 删除（根据ID 批量删除）
     *
     * @param idList 主键ID列表
     */
    default boolean removeByIds(Collection<? extends Serializable> idList) {
        if (CollectionUtils.isEmpty(idList)) {
            return false;
        }
        return SqlHelper.retBool(getBaseMapper().deleteBatchIds(idList));
    }

    /**
     * 根据 ID 选择修改
     *
     * @param entity 实体对象
     */
    default boolean updateById(T entity) {
        return SqlHelper.retBool(getBaseMapper().updateById(entity));
    }

    /**
     * 根据 UpdateWrapper 条件，更新记录 需要设置sqlset
     *
     * @param updateWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.update.UpdateWrapper}
     */
    default boolean update(Wrapper<T> updateWrapper) {
        return update(null, updateWrapper);
    }

    /**
     * 根据 whereEntity 条件，更新记录
     *
     * @param entity        实体对象
     * @param updateWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.update.UpdateWrapper}
     */
    default boolean update(T entity, Wrapper<T> updateWrapper) {
        return SqlHelper.retBool(getBaseMapper().update(entity, updateWrapper));
    }

    /**
     * 根据ID 批量更新
     *
     * @param entityList 实体对象集合
     */
    @Transactional(rollbackFor = Exception.class)
    default boolean updateBatchById(Collection<T> entityList) {
        return updateBatchById(entityList, DEFAULT_BATCH_SIZE);
    }

    /**
     * 根据ID 批量更新
     *
     * @param entityList 实体对象集合
     * @param batchSize  更新批次数量
     */
    boolean updateBatchById(Collection<T> entityList, int batchSize);

    /**
     * TableId 注解存在更新记录，否插入一条记录
     *
     * @param entity 实体对象
     */
    boolean saveOrUpdate(T entity);

    /**
     * 根据 ID 查询
     *
     * @param id 主键ID
     */
    default T getById(Serializable id) {
        return getBaseMapper().selectById(id);
    }

    /**
     * 查询（根据ID 批量查询）
     *
     * @param idList 主键ID列表
     */
    default List<T> listByIds(Collection<? extends Serializable> idList) {
        return getBaseMapper().selectBatchIds(idList);
    }

    /**
     * 查询（根据 columnMap 条件）
     *
     * @param columnMap 表字段 map 对象
     */
    default List<T> listByMap(Map<String, Object> columnMap) {
        return getBaseMapper().selectByMap(columnMap);
    }

    /**
     * 根据 Wrapper，查询一条记录 <br/>
     * <p>结果集，如果是多个会抛出异常，随机取一条加上限制条件 wrapper.last("LIMIT 1")</p>
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    default T getOne(Wrapper<T> queryWrapper) {
        return getOne(queryWrapper, true);
    }

    /**
     * 根据 Wrapper，查询一条记录
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     * @param throwEx      有多个 result 是否抛出异常
     */
    T getOne(Wrapper<T> queryWrapper, boolean throwEx);

    /**
     * 根据 Wrapper，查询一条记录
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    Map<String, Object> getMap(Wrapper<T> queryWrapper);

    /**
     * 根据 Wrapper，查询一条记录
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     * @param mapper       转换函数
     */
    <V> V getObj(Wrapper<T> queryWrapper, Function<? super Object, V> mapper);

    /**
     * 查询总记录数
     *
     * @see Wrappers#emptyWrapper()
     */
    default int count() {
        return count(Wrappers.emptyWrapper());
    }

    /**
     * 根据 Wrapper 条件，查询总记录数
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    default int count(Wrapper<T> queryWrapper) {
        return SqlHelper.retCount(getBaseMapper().selectCount(queryWrapper));
    }

    /**
     * 查询列表
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    default List<T> list(Wrapper<T> queryWrapper) {
        return getBaseMapper().selectList(queryWrapper);
    }

    /**
     * 查询所有
     *
     * @see Wrappers#emptyWrapper()
     */
    default List<T> list() {
        return list(Wrappers.emptyWrapper());
    }

    /**
     * 翻页查询
     *
     * @param page         翻页对象
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    default <E extends IPage<T>> E page(E page, Wrapper<T> queryWrapper) {
        return getBaseMapper().selectPage(page, queryWrapper);
    }

    /**
     * 无条件翻页查询
     *
     * @param page 翻页对象
     * @see Wrappers#emptyWrapper()
     */
    default <E extends IPage<T>> E page(E page) {
        return page(page, Wrappers.emptyWrapper());
    }

    /**
     * 查询列表
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    default List<Map<String, Object>> listMaps(Wrapper<T> queryWrapper) {
        return getBaseMapper().selectMaps(queryWrapper);
    }

    /**
     * 查询所有列表
     *
     * @see Wrappers#emptyWrapper()
     */
    default List<Map<String, Object>> listMaps() {
        return listMaps(Wrappers.emptyWrapper());
    }

    /**
     * 查询全部记录
     */
    default List<Object> listObjs() {
        return listObjs(Function.identity());
    }

    /**
     * 查询全部记录
     *
     * @param mapper 转换函数
     */
    default <V> List<V> listObjs(Function<? super Object, V> mapper) {
        return listObjs(Wrappers.emptyWrapper(), mapper);
    }

    /**
     * 根据 Wrapper 条件，查询全部记录
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    default List<Object> listObjs(Wrapper<T> queryWrapper) {
        return listObjs(queryWrapper, Function.identity());
    }

    /**
     * 根据 Wrapper 条件，查询全部记录
     *
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     * @param mapper       转换函数
     */
    default <V> List<V> listObjs(Wrapper<T> queryWrapper, Function<? super Object, V> mapper) {
        return getBaseMapper().selectObjs(queryWrapper).stream().filter(Objects::nonNull).map(mapper).collect(Collectors.toList());
    }

    /**
     * 翻页查询
     *
     * @param page         翻页对象
     * @param queryWrapper 实体对象封装操作类 {@link com.baomidou.mybatisplus.core.conditions.query.QueryWrapper}
     */
    default <E extends IPage<Map<String, Object>>> E pageMaps(E page, Wrapper<T> queryWrapper) {
        return getBaseMapper().selectMapsPage(page, queryWrapper);
    }

    /**
     * 无条件翻页查询
     *
     * @param page 翻页对象
     * @see Wrappers#emptyWrapper()
     */
    default <E extends IPage<Map<String, Object>>> E pageMaps(E page) {
        return pageMaps(page, Wrappers.emptyWrapper());
    }

    /**
     * 获取对应 entity 的 BaseMapper
     *
     * @return BaseMapper
     */
    BaseMapper<T> getBaseMapper();

    /**
     * 以下的方法使用介绍:
     *
     * 一. 名称介绍
     * 1. 方法名带有 query 的为对数据的查询操作, 方法名带有 update 的为对数据的修改操作
     * 2. 方法名带有 lambda 的为内部方法入参 column 支持函数式的
     *
     * 二. 支持介绍
     * 1. 方法名带有 query 的支持以 {@link ChainQuery} 内部的方法名结尾进行数据查询操作
     * 2. 方法名带有 update 的支持以 {@link ChainUpdate} 内部的方法名为结尾进行数据修改操作
     *
     * 三. 使用示例,只用不带 lambda 的方法各展示一个例子,其他类推
     * 1. 根据条件获取一条数据: `query().eq("column", value).one()`
     * 2. 根据条件删除一条数据: `update().eq("column", value).remove()`
     *
     */

    /**
     * 链式查询 普通
     *
     * @return QueryWrapper 的包装类
     */
    default QueryChainWrapper<T> query() {
        return ChainWrappers.queryChain(getBaseMapper());
    }

    /**
     * 链式查询 lambda 式
     * <p>注意：不支持 Kotlin </p>
     *
     * @return LambdaQueryWrapper 的包装类
     */
    default LambdaQueryChainWrapper<T> lambdaQuery() {
        return ChainWrappers.lambdaQueryChain(getBaseMapper());
    }

    /**
     * 链式更改 普通
     *
     * @return UpdateWrapper 的包装类
     */
    default UpdateChainWrapper<T> update() {
        return ChainWrappers.updateChain(getBaseMapper());
    }

    /**
     * 链式更改 lambda 式
     * <p>注意：不支持 Kotlin </p>
     *
     * @return LambdaUpdateWrapper 的包装类
     */
    default LambdaUpdateChainWrapper<T> lambdaUpdate() {
        return ChainWrappers.lambdaUpdateChain(getBaseMapper());
    }

    /**
     * <p>
     * 根据updateWrapper尝试更新，否继续执行saveOrUpdate(T)方法
     * 此次修改主要是减少了此项业务代码的代码量（存在性验证之后的saveOrUpdate操作）
     * </p>
     *
     * @param entity 实体对象
     */
    default boolean saveOrUpdate(T entity, Wrapper<T> updateWrapper) {
        return update(entity, updateWrapper) || saveOrUpdate(entity);
    }

~~~







