



## **Spring boot**

 



### **1、解析JSON数据**

 

使用FastJson转JSON格式

```java
//第一种方法: 在启动类让其继承WebMvcConfigurerAdapter。
//然后重新实现方法configureMessageConverters。

// 启动类
@SpringBootApplication
public class SpringBootApp extends WebMvcConfigurerAdapter{

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        super.configureMessageConverters(converters);

        // 1、定义fastJson的转换器
        FastJsonHttpMessageConverter converter = new FastJsonHttpMessageConverter();
        // 2、配置fastJson
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        fastJsonConfig.setSerializerFeatures(
                SerializerFeature.PrettyFormat // 格式化
        );

        // 最后添加到总的转换器中
        converter.setFastJsonConfig(fastJsonConfig);
        converters.add(converter);

    }

    public static void main(String[] args) {
        SpringApplication.run(SpringBootApp.class, args);
    }
}


```



```java
//第二种方式: 采用@Bean注解自定义实现


@Configuration
public class CustomFastJsonConfig {

    @Bean
    public HttpMessageConverters fastJsonHttpMessageConverter(){
        // FastJson转换器
        FastJsonHttpMessageConverter converter = new FastJsonHttpMessageConverter();
        FastJsonConfig fastJsonConfig = new FastJsonConfig();

        fastJsonConfig.setSerializerFeatures(
                SerializerFeature.PrettyFormat
        );

        converter.setFastJsonConfig(fastJsonConfig);
        return  new HttpMessageConverters(converter);
    }
}


```



*FastJson*中JSONField的用法

```java
/**
 * FastJson中 JSONField
 *      format 格式转换输出
 *      serialize = false 表示不输出此字段
 */
@JSONField(format = "yyyy-MM-dd HH:mm:ss")
private Date birth ;

@JSONField(serialize = false)
private  String remark ;


```



 

 









### **2、热部署devtools**

```xml
1、首先添加依赖
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
    <scope>true</scope>
</dependency>

2、添加插件
<!--构建节点-->
<build>
    <plugins>
        <!--重新打包 热部署插件 -->
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <!--fork :  如果没有该项配置则devtools不会起作用，即应用不会restart -->
                <fork>true</fork>
            </configuration>
        </plugin>
    </plugins>
</build>

3、然后修改类文件、配置文件等，应用都会重启。但是它的重启方式比手工重启效率快很多。
其原理是使用的两个ClassLoader加载。一个ClassLoader加载那些不会改变的类(第三方JAR)，另一个ClassLoader加载会改变的类，称为restart ClassLoader。
这样在有代码更改的时候，原来的restart ClassLoader被丢弃，重新创建一个新的restart ClassLoader，由于需要加载的类比较少，所以实现了较快的重启。

4、有可能配置了还是无效，则需配置编译器自动编译。

```



 

 

### **3、JPA**

 

   **JPA**全称(java persistence API)，是SUN公司官方提出的**Java持久化规范**。

它为Java开发人员提供了一种对象/关系映射工具来管理Java应用程序中的关系数据。   

**持久化**即把数据(内存中的数据)保存到可永久保存的存储设备中。   

 

 