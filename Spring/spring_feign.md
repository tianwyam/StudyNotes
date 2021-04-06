### Feign



使用feign，实现请求第三方接口，作为一个 http请求工具.

常在springboot项目中使用feign，作为HTTP请求工具，类似Rest Template，也可以单独作为工具类使用

1. 在springboot项目中使用feign，作为HTTP请求工具，类似 Rest Template
2. 在spring、springMVC环境下使用，也可以单独使用，类似  http client



### 在spring boot环境体系下使用



第一步，在maven 引入依赖，pom.xml

~~~xml
<!-- 用于springboot项目环境下，或者springcloud项目环境下 集成 feign -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
    <version>2.0.2.RELEASE</version>
</dependency>
~~~

第二步，编写feign客户端，相当于实际请求接口

只需要定义方法请求，类似服务端controller的请求，url配置可以使用springcloud体系里面微服务配置

~~~java
@FeignClient(name = "studentFeignClient", url = "${student.feign.url}")
public interface StudentFeignClient {

    @GetMapping("/list")
    public ResultUtis getList() ;
}
~~~

第三步，在启动类上注册扫描

~~~java
// 开启 feign
@EnableFeignClients
@SpringBootApplication
public class FeignLearnApplication {

    public static void main(String[] args) {
        SpringApplication.run(FeignLearnApplication.class, args);
    }
}
~~~



第四步，客户端实际方法中调用服务端接口

~~~java
@RestController
@RequestMapping("/student")
public class StudentController {
    
    // 实际调用，自动注入
    @Autowired
    private StudentFeignClient studentFeignClient ;

    @GetMapping("/list")
    public ResultUtis getList() {
        return studentFeignClient.getList();
    }
}
~~~

以往使用Rest Template的做法：

~~~java

@RestController
@RequestMapping("/student")
public class StudentController {
	
	@Autowired
	private RestTemplate restTemplate ;
	
    // 学生服务端接口URL
	@Value("${student.feign.url}")
	private String studentServerUrl ;
	
	@GetMapping("/list")
	public ResultUtis getList() {
		// 类似
		return restTemplate.getForObject(studentServerUrl + "/list", 
                                         ResultUtis.class);
	}
	
}
~~~

相比较下，feign请求访问服务接口更加简洁，更加方便





#### 不在springboot体系下



feign可以在spring、springMVC环境下使用，也可以单独使用，类似  http client



第一步，配置maven，添加JAR包，pom.xml

~~~xml
<!-- 以下几个用于 spring环境、springmvc环境下的，或者 不是 spring容器内的项目 集成 feign -->
<!-- feign-core 是核心，其他JAR用不到，可以不用引入 -->
 <dependency>
     <groupId>io.github.openfeign</groupId>
     <artifactId>feign-core</artifactId>
    <version>${feign.version}</version>
</dependency>

 <dependency>
     <groupId>io.github.openfeign</groupId>
     <artifactId>feign-gson</artifactId>
    <version>${feign.version}</version>
</dependency>

 <dependency>
     <groupId>io.github.openfeign</groupId>
     <artifactId>feign-jackson</artifactId>
    <version>${feign.version}</version>
</dependency>



 <dependency>
     <groupId>io.github.openfeign</groupId>
     <artifactId>feign-httpclient</artifactId>
    <version>${feign.version}</version>
</dependency>


 <dependency>
     <groupId>io.github.openfeign</groupId>
     <artifactId>feign-okhttp</artifactId>
    <version>${feign.version}</version>
</dependency>

 <dependency>
     <groupId>io.github.openfeign</groupId>
     <artifactId>feign-ribbon</artifactId>
    <version>${feign.version}</version>
</dependency>
~~~



第二步，编写feign客户端，实际访问请求接口

```java
/**
 * @description
 *	不在spring环境下的  feign 接口定义
 * @author TianwYam
 * @date 2020年12月25日下午6:25:42
 */
public interface StudentFeignClientNoSpring {
    

    // 不使用spring环境下的，只能使用 feign自带的 RequestLine 来声明 方法 请求 类型
    @RequestLine("GET /list")
    public ResultUtis getList() ;

}
```

第三步，调用方式

```java
/**
 * @description
 *	在不是 spring环境下 、 springboot环境下的 使用feign
 * @author TianwYam
 * @date 2020年12月25日下午6:17:56
 */
public class FeignNoSpringMain {

    public static void main(String[] args) {
        
        
        StudentFeignClientNoSpring studentFeignClient = Feign.builder()
            .decoder(new GsonDecoder())
            .target(StudentFeignClientNoSpring.class, "http://localhost:8081/api/v1/student");
        
        ResultUtis list = studentFeignClient.getList();
        
        System.out.println(JSON.toJSONString(list, SerializerFeature.PrettyFormat));
    }
    
}
```

此处写成main方式，实际项目中，可以写成一个工具类

```java
/**
 * @description
 *	Feign工具类
 * @author TianwYam
 * @date 2021年1月30日上午11:24:21
 */
public class FeignClientUtils {

    /**
     * @description
     *	获取 feign客户端
     * @author TianwYam
     * @date 2021年1月30日上午11:23:34
     * @param <T> 客户端类
     * @param url 调用客户端请求的URL
     * @param clazz 客户端 class
     * @return
     */
    public static <T> T getClient(String url, Class<T> clazz) {

        return Feign.builder()
                .decoder(new GsonDecoder())
                .encoder(new GsonEncoder())
                .target(clazz, url);
    }
}
```



相对于httpclient下方便许多，不需要复杂的去设置 请求方式get\post等，也不需要对返回值做解析

feign是直接跟服务端的controller保持一致，定义请求方法，相对来说更加简单，更加容易





