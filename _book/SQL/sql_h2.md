





## H2内嵌数据库的使用



​	**H2**是一个开源的嵌入式数据库引擎，采用java语言编写，不受平台的限制。

​	同时H2提供了一个十分方便的web控制台用于操作和管理数据库内容。

​	H2还提供兼容模式，可以兼容一些主流的数据库，因此采用H2作为开发期的数据库非常方便。 

​	H2作为一个嵌入型的数据库，它最大的好处就是可以嵌入到我们的Web应用中，和我们的Web应用绑定在一起，成为我们Web应用的一部分。





### 运行方式

H2数据库有三种运行方式实现：

1. 嵌入式(embedded)：可以同应用程序打包在一起发布，这样可以非常方便地存储少量结构化数据
2. 服务模式：
	1.TCP/IP server：支持客户端/服务器端的连接方式
	2.web server：此种运行方式支持使用浏览器访问H2 Console
	3.PG server：支持PostgreSQL客户端
3. 内存方式：可以作为缓存，作为NoSQL的一个补充。
当某些场景下数据模型必须为关系型，可以拿它当Memcached使，作为后端MySQL/Oracle的一个缓冲层，缓存一些不经常变化但需要频繁访问的数据，比如字典表、权限表。



### JDBC URL



1、内嵌模式不用启动服务，已内嵌，不用显示启动服务

~~~java
String jdbcURL = "jdbc:h2:~/h2/db";
~~~



2、TCP/IP server 服务器模式，必须要显示启动服务

~~~java
String jdbcURL = "jdbc:h2:tcp://localhost/~/h2/db";
~~~

3、内存模式 

~~~java
String jdbcURL = "jdbc:h2:mem:h2db";

// 或者

String jdbcURL = "jdbc:h2:tcp://localhost/mem:h2db";
~~~



------



## Java应用中的使用



### 操作实例

H2数据库基本操作：

~~~java
// ~ 用户目录C:\Users\用户名\
String jdbcURL = "不同模式下，不同的jdbcURL，其他操作一样";

//连接数据库时使用的用户名
final String user = "tianya";

//连接数据库时使用的密码
String password = "123456";

//连接H2数据库时使用的驱动类
//org.h2.Driver 
String driverClass="org.h2.Driver";

try {
	
	// 1、加载驱动
	Class.forName(driverClass);
	
	// 2、获取连接
	Connection connection = DriverManager.getConnection(jdbcURL, user, password);
	Statement statement = connection.createStatement();
	
	// 3、执行操作
	// 3.1、先删除表，若存在
	statement.execute("drop table user_info if exists ");
    
	// 3.2、创建表
	statement.execute("create table user_info(id int primary key, name varchar(10), age int , sex varchar(2) )");
	
	// 4、新增
	statement.executeUpdate("insert into user_info(id,name,age,sex) values(1,'张三',23,'男' )");
	statement.executeUpdate("insert into user_info(id,name,age,sex) values(2,'李四',25,'男' )");
	statement.executeUpdate("insert into user_info(id,name,age,sex) values(3,'王五',33,'男' )");
	statement.executeUpdate("insert into user_info(id,name,age,sex) values(4,'珠帘',23,'女' )");
	statement.executeUpdate("insert into user_info(id,name,age,sex) values(5,'鲤鱼',20,'女' )");
	
	// 5、查询
	ResultSet rs = statement.executeQuery("select * from user_info");
	while (rs.next()) {
		System.out.println(rs.getInt(1) + " - " + rs.getString(2) 
		+ " - " + rs.getInt(3)+ " - " + rs.getString(4) );
	}
	
	// 释放资源
	statement.close();
	connection.close();
	
} catch (Exception e) {
	e.printStackTrace();
}
~~~





------





## Java web 应用中的使用



### H2服务的启动



#### 1.命令行启动服务

```bash
java -cp h2*.jar org.h2.tools.Server -?

常见的选项如下：

-web：启动支持H2 Console的服务
-webPort <port>：服务启动端口，默认为8082
-browser：启动H2 Console web管理页面
-tcp：使用TCP server模式启动
-pg：使用PG server模式启动

```

如：

~~~bash
## 浏览器web服务方式
java -jar h2*.jar org.h2.tools.Server -web -webPort 8082 -browser 

## TCP服务方式
java -jar h2*.jar org.h2.tools.Server -tcp -tcpPort 9092 -tcpSSL 
~~~









#### 2.Servlet的方式



注解的方式

~~~java
package com.tianya.mw.web;

import java.sql.SQLException;
import java.util.HashMap;
import java.util.Map;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.ServletRegistration.Dynamic;
import javax.servlet.annotation.WebListener;

import org.apache.log4j.Logger;
import org.h2.server.web.WebServlet;
import org.h2.tools.Server;

/**
 * Copyright: Copyright (c) 2019 tianwyam
 * 
 * @ClassName: H2DBServerListener.java
 * @Description: 在WEB应用中启动H2数据库服务的监听器
 * @version: v1.0.0
 * @author: tianwyam
 * @date: 2019年3月18日 上午9:56:21
 */
@WebListener
public class H2DBServerListener implements ServletContextListener {

	private transient static final Logger log = Logger.getLogger(H2DBServerListener.class);
    // H2 DB 服务
	private Server server;
	
    // H2 tcp访问的端口
	public static final int H2_DB_SERVER_PORT = 8082 ;

	@Override
	public void contextInitialized(ServletContextEvent event) {
		
		
		try {
            
            // 启动H2数据库服务
			log.info("启动H2数据库...");
			log.info(String.format("TCP客户端访问端口：%s", H2_DB_SERVER_PORT));
			// 默认端口为8082
			server = Server.createTcpServer(
					"-tcpPort", 
					String.valueOf(H2_DB_SERVER_PORT), 
					"-tcpAllowOthers").start();
			log.info("H2数据库启动成功...");
			

            // 注解方式 添加 H2 DB console 访问 WebServlet
			// 注册 H2数据库 web 控制台  
			// 添加 org.h2.server.web.WebServlet
			ServletContext servletContext = event.getServletContext();
			Dynamic webServlet = servletContext.addServlet("H2Console", WebServlet.class);
			// 控制台 访问路径
			webServlet.addMapping("/console/*");
			webServlet.setLoadOnStartup(1);
			// 设置配置
			Map<String, String> initParameters = new HashMap<>();
			initParameters.put("allowOthers", "true");
			initParameters.put("trace", "true");
			webServlet.setInitParameters(initParameters);
			
			log.info("H2 CONSOLE 默认访问URL：http://localhost:8080/[project_name]/console/");
			
			
		} catch (SQLException e) {
			log.error("H2数据库启动失败！", e);
		}

	}

	@Override
	public void contextDestroyed(ServletContextEvent event) {

		// 停止服务
		if (server != null) {
			log.info("关闭H2数据库...");
			server.shutdown();
			log.info("关闭H2数据库成功...");
		}
	}

}

~~~



web.xml配置方式

~~~xml

<!-- 注册服务监听 -->
<listener>
	<listener-class>com.tianya.mw.web.H2DBServerListener</listener-class>
</listener>

<!-- 使用H2控制台的Servlet H2控制台是一个独立的应用程序，
包括它自己的Web服务器，但它可以作为一个servlet作为-->
<servlet>
    <servlet-name>H2Console</servlet-name>
    <servlet-class>org.h2.server.web.WebServlet</servlet-class>
     <init-param>
        <param-name>webAllowOthers</param-name>
        <param-value></param-value>
    </init-param>
    <init-param>
        <param-name>trace</param-name>
        <param-value></param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

<!-- 映射H2控制台的访问路径 -->
<servlet-mapping>
    <servlet-name>H2Console</servlet-name>
    <url-pattern>/console/*</url-pattern>
</servlet-mapping>

~~~



访问 查看是否服务启动成功

~~~http
http://localhost:8080/[project_name]/console/
~~~





#### 3.maven插件方式

~~~xml

<build>

	<finalName>LearnH2DB</finalName>
	
	<!-- maven插件方式启动 H2 DB 服务 -->
	<plugins>
		<plugin>
			<groupId>org.codehaus.mojo</groupId>
			<artifactId>exec-maven-plugin</artifactId>
			<executions>
				<execution>
					<goals>
						<goal>java</goal>
					</goals>
				</execution>
			</executions>
			<configuration>
				<mainClass>org.h2.tools.Server</mainClass>
				<arguments>
					<argument>-web</argument>
					<argument>-webPort</argument>
					<argument>8082</argument>
					<argument>-browser</argument>
				</arguments>
			</configuration>
		</plugin>
	</plugins>
    
</build>

~~~

执行命令

~~~bash
mvn exec:java
~~~



相当于

~~~bash
java -jar h2*.jar org.h2.tools.Server -web -webPort 8082 -browser
~~~



------



### 数据库初始化



#### 1.maven方式

~~~xml
<!-- 初始化 配置  -->
<profiles>
	<profile>
		<id>init-db</id>
		<build>
			<plugins>
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-antrun-plugin</artifactId>
					<version>1.8</version>
					<configuration>
						<target>
							<property file="src/main/resources/h2_jdbc.properties" />
							<sql driver="${jdbc.driver}" 
								url="${jdbc.url}" 
								userid="${jdbc.username}"
								password="${jdbc.password}" 
								onerror="continue"
								encoding="${project.build.sourceEncoding}">
								<classpath refid="maven.test.classpath" />
								<transaction src="src/main/resources/sql/h2/schema.sql" />
								<transaction src="src/main/resources/sql/h2/data.sql" />
							</sql>
						</target>
					</configuration>
				</plugin>
			</plugins>
		</build>
	</profile>
</profiles>

~~~

执行命令：

~~~bash
mvn antrun:run -P init-db
~~~







#### 2.spring方式

~~~xml
<beans profile="test">
	<context:property-placeholder ignore-resource-not-found="true"
		location="classpath*:/*_jdbc.properties" />    
	
	<!-- Spring Simple连接池 -->
	<bean id="dataSource" class="org.springframework.jdbc.datasource.SimpleDriverDataSource">
		<property name="driverClass" value="${jdbc.driver}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
	</bean>

	<!-- 初始化数据表结构 -->
	<jdbc:initialize-database data-source="dataSource" ignore-failures="ALL">
		<jdbc:script location="classpath:sql/h2/schema.sql" />
		<jdbc:script location="classpath:sql/h2/data.sql" encoding="UTF-8"/>
	</jdbc:initialize-database>
</beans>
~~~


