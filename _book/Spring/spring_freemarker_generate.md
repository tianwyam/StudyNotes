## Free marker自动生成Java bean代码



项目中经常会遇到根据数据库表，编写Java bean，一旦表多，字段多，就很麻烦，所以需要自动生成代码

学习使用free marker模板语言来自动生成代码





### 1. 导入freemarker依赖



pom.xml

~~~xml
<!-- spring boot web -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- spring boot 集成 freemarker -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
~~~



由于我这个项目是spring boot工程，所以导入的是spring boot相关集成依赖

若是只用free marker，则导入：

~~~xml
<dependency>
  <groupId>org.freemarker</groupId>
  <artifactId>freemarker</artifactId>
  <version>2.3.29</version>
</dependency>
~~~





### 2. 编写生成Java类所需要的描述



新建一个类，来存放生成一个Java类的所有描述

~~~java
package com.tianya.springboot.freemarker.code.entity;

import java.util.List;

import lombok.Builder;
import lombok.Data;

/**
 * @Description: 
 * Java Bean自动生成
 * @author: TianwYam
 * @date 2021年5月5日 上午9:12:40
 */
@Data
@Builder
public class JavaBeanGenerate {
	
	// 包名
	private String packageName ;
	
	// 类名
	private String className ;
	
	// 属性
	private List<JavaBeanAttribute> attributes ;
	
	// 需要导入的包
	private List<String> importClassList ;
	
	// 生成的文件需要保存的全路径
	private String path ;

}
~~~



### 3. 编写描述Bean的属性类



描述 Java bean中属性的

~~~java
package com.tianya.springboot.freemarker.code.entity;

import lombok.Builder;
import lombok.Data;

/**
 * @Description: 
 * Java Bean的属性
 * @author: TianwYam
 * @date 2021年5月5日 上午9:15:01
 */
@Data
@Builder
public class JavaBeanAttribute {
	
	// 属性名称
	private String attrName ;
	
	// 类型
	private String attrType ;
	
	// 注释
	private String attrComment;
	
}
~~~



### 4. 制定生成Java类的模板



javaBeanGenerate.ftl 描述生成的Java类的free marker模板

~~~velocity
package ${packageName} ;

<#if importClassList?? && (importClassList?size > 0) >
<#list importClassList as importClass>
import ${importClass} ;
</#list>
</#if>

import lombok.Data ;


@Data
public class ${className} {

<#if attributes?? && (attributes?size > 0) >
	<#list attributes as attr>
	
		// ${attr.attrComment}
		private ${attr.attrType} ${attr.attrName} ;
		
	</#list>
</#if>


}
~~~



### 5. 测试生成Java类



此处模拟的数据，直接写死的，实际情况，应该读取数据库，根据建表语句生成

~~~java

/**
 * @Description: 
 * 自动生成代码 工具类
 * @author: TianwYam
 * @date 2021年5月5日 上午9:11:42
 */
public class AutoGenerateCodeUtils {

	/**
	 * @Description: 
	 * 生成JavaBean代码
	 * @author: TianwYam
	 * @date 2021年5月5日 上午9:53:27
	 */
	public static void generateCode() {
		
        // 测试数据，模拟用的
        // 实际项目中，应该读取数据库
        
		//定义属性
		List<JavaBeanAttribute> attributes = new ArrayList<>();
		attributes.add(JavaBeanAttribute.builder().attrName("id").attrType("int").attrComment("主键").build());
		attributes.add(JavaBeanAttribute.builder().attrName("name").attrType("String").attrComment("教师姓名").build());
		attributes.add(JavaBeanAttribute.builder().attrName("sex").attrType("int").attrComment("性别").build());
		attributes.add(JavaBeanAttribute.builder().attrName("birthDate").attrType("Date").attrComment("出生日期").build());
		
		// Java bean 生成 teacher 老师
		JavaBeanGenerate generate = JavaBeanGenerate.builder()
				.className("Teacher")
				.packageName("com.tianya.springboot.freemarker.code")
				.attributes(attributes)
				.importClassList(Arrays.asList("java.util.Date"))
				.path("D:\\javaWork\\spring-boot-learn\\spring-boot-learn-freemarker\\src\\main\\java\\com\\tianya\\springboot\\freemarker\\code\\Teacher.java")
				.build();
		
		// 生成Java bean
		FreeMarkerUtils.fillTemplate(generate.getPath(), "javaBeanGenerate.ftl", generate);
		
	}
	
	
	
	
	public static void main(String[] args) {
		
		generateCode();
		
	}
	
}
~~~



FreeMarkerUtils 工具类，根据模板文件，填充数据

~~~java
/**
 * @Description: 
 *  填充模板，输出文件
 * @author: TianwYam
 * @date 2021年5月4日 上午9:41:37
 * @param outputFilePath 填充后的文件输出的全路径
 * @param templateName 模板的名称
 * @param data 需要填充的数据
 */
public static void fillTemplate(String outputFilePath, String templateName, Object data) {
	
	Template template = getTemplate(templateName);
	if (template != null) {
		try {
			template.process(data, new FileWriter(outputFilePath));
			LOG.info("生成word文件成功");
		} catch (TemplateException | IOException e) {
			LOG.error("生成word文件失败", e);
		}
	}
	
}
~~~



### 6. 生成Java类效果



Teacher 类

~~~java
package com.tianya.springboot.freemarker.code ;

import java.util.Date ;

import lombok.Data ;


@Data
public class Teacher {

	
		// 主键
		private int id ;
		
	
		// 教师姓名
		private String name ;
		
	
		// 性别
		private int sex ;
		
	
		// 出生日期
		private Date birthDate ;
		
}
~~~

