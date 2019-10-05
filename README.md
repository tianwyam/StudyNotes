# 学习知识汇总





## Java

 

### 集合



[java集合-java_Map_HashMap & HashTable & ConcurrentHashMap](java/java_collection_map.md)




### **队列**

 

[java队列-java_queue](java/java_queue.md)



### **IO**

 

[java输入输出流-java-io-nio](java/java_io_nio.md)



### **异常**

 

 [java异常exception-java_exception](java/java_exception.md)





### **多线程**

 

[java多线程-java_multi_thread_volatile_synchronized](java/java_multi_thread_volatile_synchronized.md)

[java多线程-java_multi_thread_object_wait_notify](java/java_multi_thread_object_wait_notify.md)

[java多线程-java_thread](java/java_multi_thread_thread.md)

[java多线程-java_lock_ReentrantLock_ReadWriteLock](java/java_multi_thread_lock.md)

[Java多线程-java_lock_condition](java/java_multi_thread_lock_condition.md)

 [java多线程-Java并发工具- CountDownLatch & CyclicBarrier ](java/java_multi_thread_tools.md)







### **JVM**

 

#### **垃圾回收**

 

##### **分代回收方式**

 

   **分代回收**是垃圾回收中的一种常见算法。特点是把内存划分成不同的世代，分别对应虚拟机中存活时间不同的对象。对于存活时间不同的对象，可以采用不同的回收策略。       

​	对于包含存活时间较短的对象的内存空间，其中所包含的存活对象较少，可以被回收的区域比较多，而且状态变化比较快，因此对这个世代的内存进行回收的频率比较高，速度较快。而对于存活时间比较长的对象，回收的频率可以比较低。       

​	在HotSpot虚拟机中，把内存划分为3个世代：**年轻**、**年老**、**永久**。大部分对象所需内存的分配都是在年轻代区域进行的。当垃圾回收器运行时，**年轻代**中的很多对象可能已经不再存活，可以直接被回收。而有些对象可能仍然处于存活状态。某些对象可能经过若干个垃圾回收操作后，仍然处于存活状态，对于这些对象，垃圾回收器会把这些对象移动到**年老代**的内存区域。**永久代**中包含的是java虚拟机自身运行所需的对象。       

​	**年轻代**有划分为：一个伊甸园(**eden)**、两个存活区(**survivor** space)。       

​	大部分内存分配都是在伊甸园(**eden**)中进行的，由于伊甸园的内存空间较小，因此某些所需内存较大的对象无法直接在伊甸园中进行分配，而直接在年老代中进行。   

​	两个存活区(**survivor**   space)中总有一个是空白的，在对年轻代进行垃圾回收时，先把伊甸园中的存活对象复制到当前空白的存活区中，接着对另一个非空白的存活区中的存活对象进行处理。如果对象的存活时间较短，那么同样将其复制到空白的存活区中；如果对象的存活时间较长，则复制到年老代区域。在复制到空白的存活区过程中，如果发现该存活区已经满了，那么将其复制到年老代区域。经过这两次复制后，就可以把伊甸园和非空白的存活区中的内容直接全部清空，因为这两个区域中的对象要么不再存活，要么已复制到其他内存区域中。   

​	在完成垃圾回收后，下一次的内存分配可以继续从空白的伊甸园开始进行，而两个存活区的作用也发生了交换。   

 

对于年老代和永久代内存区域，通常采用的是另一种回收算法: **标记清除压缩算法**。

 

##### **标记清除压缩算法**

 

   **标记清除压缩算法**分为三个步骤：标记(mark)、清除(sweep)、压缩(compact).   

​	第一个步骤的作用是扫描整个内存区域，把当前仍然存活的对象**标记**出来；   

​	第二个步骤的作用则是清理内存区域，**清除**垃圾；   

​	第三个步骤的作用是**压缩**整个内存区域，把存活对象所占的内存都移动到内存区域的起始位置，使内存中可用区域是连续的。   

​	经过压缩后，在年老代和永久代中进行内存分配就变得很容易，只需要从可用区域的开头位置进行分配即可。   

 

 

 

 

------





### **Java7新知识**

 

#### **Switch**

 

   Java中switch支持char\int\byte\short及它们的封装类类型，还有枚举类型。   

​	Java7中额外新增了String类型，这个新特性是编译器这个层次实现的。而在虚拟机和字节代码层次下，还是只支持使用与整数类型兼容的类型。

​	**虽然开发人员在java源代码中使用的是String类型，但是在编译过程中，是将String类型替换成对应的hashcode，而case里面的值也被替换成常量的哈希值，然后再case语句中仍然要使用String的equals方法来进行字符串的判断。**   

 

如: Java源代码

   ~~~java
public static void xx(String key){
		String sigal = "" ;
		switch (key) {
		case "大于":
			sigal = "大于 > " ;
			break;
		case "小于":
			sigal = "小于 > " ;
			break;
		case "等于":
			sigal = "等于 > " ;
			break;
		default:
			sigal = " 错误 " ;
			break;
		}
		System.out.println(sigal);
	}

   ~~~





编译后

  ~~~java
public static void xx(String key) {
      String sigal;
      label23: {
         sigal = "";
         switch(key.hashCode()) {
         case 727623:
            if(key.equals("大于")) {
               sigal = "大于 > ";
               break label23;
            }
            break;
         case 750687:
            if(key.equals("小于")) {
               sigal = "小于 > ";
               break label23;
            }
            break;
         case 998501:
            if(key.equals("等于")) {
               sigal = "等于 > ";
               break label23;
            }
         }
         sigal = " 错误 ";
      }
      System.out.println(sigal);
   }

  ~~~





#### **TWR**

 

   **TWR:**   try-with-resources，能够被try语句所管理的资源需要满足一个条件，就是其资源java类要实现**AutoCloseable**接口，否则会出现编译错误。

当需要释放资源的时候，该接口的close方法会被自动调用。   

 

代码

```java

public void twr(){
	// 可自动关闭
	try(FileInputStream in = new FileInputStream("e:\\fstab");
			FileOutputStream out = new FileOutputStream("e:\\a");){
		byte[] buffer = new byte[1024];
		int len = -1 ;
		while ((len = in.read(buffer)) != -1) {
			out.write(buffer, 0, len);
		}
	} catch (Exception e) {
		e.printStackTrace();
	}
}


```



 

#### **Objects**

 

   在java.util包中新增了一个用来操作对象的工具类java.util.Objects。

**Objects**类中包含的都是静态方法，可进行快速对对象操作。   

​	**compare**方法:两对象比较操作。   

​	**equals**方法:判断两个对象是否相等，若两个都是null，则为true；若一个为空，则为false   

​	**deepEquals**方法:作用于equals相似，若参数是数组，则调用Arrays类的deepEquals进行数组比较   

​	**toString**方法:获取对象字符串形式，若参数为null，则输出null字符串。当传入参数是两个时，如第一个参数为null，则输出第二个参数当作返回值(相当于默认值)   

 

```java

@Test
public void objects(){
	
	// 判断两个对象是否相等，如有一个为null，则为false
	boolean equals = Objects.equals("a", new String("a")); // true
	boolean equals1 = Objects.equals(null, new String("a")); // false
	// 数组比较
	boolean equals2 = Objects.deepEquals(new String[]{"a","b"}, new String[]{"a","b"}); // true
	// 获取对象字符串形式
	String str = Objects.toString(null,"参数为空"); // 输出 “参数为空”
}


```



 

------

 

 

 

 







## **框架**

 

 

### **Spring boot**

 

#### **1、解析JSON数据**

 

使用FastJson转JSON格式

 ~~~java
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


 ~~~



 ~~~java
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


 ~~~



*FastJson*中JSONField的用法

~~~java

/**
 * FastJson中 JSONField
 *      format 格式转换输出
 *      serialize = false 表示不输出此字段
 */
@JSONField(format = "yyyy-MM-dd HH:mm:ss")
private Date birth ;

@JSONField(serialize = false)
private  String remark ;


~~~



 

 









#### **2、热部署devtools**

 ~~~xml
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

 ~~~



 

 

#### **3、JPA**

 

   **JPA**全称(java persistence API)，是SUN公司官方提出的**Java持久化规范**。

它为Java开发人员提供了一种对象/关系映射工具来管理Java应用程序中的关系数据。   

**持久化**即把数据(内存中的数据)保存到可永久保存的存储设备中。   

 

 

------




## **数据库**

 

### **序列**

 ~~~sql
  -- Create sequence   
  create sequence MYSQL.SEQ_GUI   
  start with 1   
  increment by 1; 
  
  下个序列：SEQ_GUI.NEXTVAL
  当前序列：SEQ_GUI.CURRVAL
  获取序列：select SEQ_GUI.NEXTVAL FROM dual ; -- oracle
 ~~~

​	

 



------





## **Linux**

 

### **常用命令**

 

#### **目录操作**

 

**mkdir**

**递归创建目录**

   ~~~shell
mkdir –p /home/java/xx   
   ~~~



 

**scp**

**远程复制**

   ~~~
scp 源地址 目的地址 
	如: scp /home (username)@IP:/home    
	scp /home 192.168.1.1:/home   
   ~~~



​       

**rm**

**删除**

  ~~~shell
 rm –rf /home/java  强制删除   
  ~~~



​       

#### **文件操作**

​       

**tail**

**动态显示**

   ~~~shell
tail –f –n 1000 xx.log   
   ~~~



 

#### **文件搜索**

 

**find 查找**

  ~~~shell
 find /etc –name test 在目录etc下按名字test查找              

​	-iname test 不区分大小写              

​	-size +100M 查找大于100M的文件(+n 大于 –n 小于 n 等于)              

​	-user admin 查找所属用户为admin的文件              

​	-cmin -5    查找5分钟内被修改过属性的文件及目录 c   change              

​	-amin -5    查找5分钟内被访问过的文件 a access              

​	-mmin -5    查找5分钟内修改内容的文件 m   modify   
  ~~~



 

**grep 搜索**

 ~~~shell
 grep ‘a’ test.txt 	# 在文件test.txt中搜索匹配a的关键字   

​	grep –A 10 –B 20 keyword   # test.txt 在文件中搜索匹配关键字并输出前20行后10行间的数据   

​	grep –i keyword test.txt  # 忽略大小写搜索匹配   

​	grep –v keywory test.txt  # 排除搜索匹配
 ~~~



 

#### **权限管理**

 

**chmod 修改权限**

  ~~~shell
 chmod –R 777 /home/java  # 递归赋予权限(及目录下所以文件及文件夹都具有权限)   
  ~~~



 

**chown 修改所属者**

  ~~~shell
 chown admin test.txt  # 改变文件test.txt的所属者为admin   
  ~~~



 

**chgrp 改变所属组**

   ~~~shell
chgrp group test.txt  # 改变文件test.txt的所属组为group   
   ~~~



 

#### **用户管理**

 

**su 切换用户**

   ~~~sh
su – admin # 切换到admin用户下   
   ~~~



 

**useradd 添加用户**

   ~~~shell
useradd admin # 新建一个admin用户   
   ~~~



​       

**userdel 添加用户**

   ~~~shell
userdel -r admin # 删除用户的同时删除用户的根目录(/home/admin)   
   ~~~



​       

**passwd 设置密码**

   ~~~shell
passwd admin  # 为admin用户设置密码   
   ~~~



​       

 

#### **压缩解压**

 

**zip 压缩**

  ~~~shell
 zip –r java.zip java   # 将java目录及目录下所以文件压缩成java.zip   
  ~~~



 

**unzip 解压**

   ~~~shell
unzip java.zip –d  ./java      # 将java.zip解压到当前目录下的java文件夹里   
   ~~~



 

**gzip 压缩**



   ~~~shell
gzip -c build.sh > build.sh.gz  # 将文件build.sh打包成build.sh.gz   
   ~~~



 





**gunzip 解压**

   ~~~shell
gunzip build.sh.gz  # 解压   
   ~~~



 

**tar** 

   ~~~shell
tar - czvf 123.tar.gz 123 打包 

​	–c 打包 

​	–z 打包并压缩 

​	–v 显示打包过程 

​	–f 指定文件名   

tar –xzvf 123.tar.gz 解压 

	–x 解包   
   ~~~



 

#### **网络**

 

**telnet查询端口是否调通**

   ~~~shell
telnet IP port   
   ~~~



 

**ping 测试网络连通性**

   ~~~shell
ping –c3 ip  c3次数   
   ~~~



 

**ifconfig 查看/设置网卡信息**

~~~shell
ifconfig  # 查看网卡信息   

ifconfig eth0 192.168.1.100  # 临时设置eth0的ip地址为192.168.1.100   
~~~



 

 

#### 建立信任

 

   实现主机A和主机B间的信任连接步骤：   

​	1）  主机A上使用命令创建密钥：**ssh –keygen –t rsa**   

​	2）  把主机A的/root/.ssh/id_ras.pub文件内容复制到主机B的/root/.ssh/authorized_keys   

​	3）  此时主机A连接主机B就不需密码了   

​	4）  相同的，把主机B公共密钥复制拷贝到主机A的/root/.ssh/authorized_keys下   

​	5）  此时主机A和主机B间就建立起信任了   

 

 

 

 






