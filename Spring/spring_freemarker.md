

## Spring Boot 集成 Freemarker



spring boot 集成 freemarker 的简单使用

使用freemarker生成word文件



[TOC]





### 1. spring boot集成freemarker





#### 1.1 添加依赖

maven项目 pom.xml

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



#### 1.2 配置freemarker

application.yml文件

~~~yaml

server:
  port: 8080
  servlet:
    context-path: /
    
#freemarker的配置
spring:
  freemarker:
    cache: false
    charset: UTF-8
    content-type: text/html; charset=utf-8
    prefix: /
    suffix: .html
~~~



freemarker 默认模板文件后缀为 .ftl，并且默认的模板存放路径为：resources/templates



#### 1.3 控制器访问页面

~~~java
@Controller
public class IndexController {
	
	@GetMapping("/index")
	public ModelAndView index() {
		
		ModelAndView view = new ModelAndView("index");
		view.addObject("user", "Tom");
		return view ;
	}
}
~~~



#### 1.4 页面



index.html

~~~html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>首页</title>
</head>
<body>
	INDEX | templates index
	<h3>hello ${user}</h3>
</body>
</html>
~~~



路径在：resources/template/index.html



#### 1.5 访问请求



~~~http
http://localhost:8080/index
~~~



界面展示如下：

![controller-index](freemarker\controller-index.png)

### 2. freemarker实现生成word文件



freemarker通过模板可以生成work文件



#### 2.1 制作word模板



首先使用word编辑工具(office / WPS)，新建word模板，根据实际情况制定模板，模拟如下：



![word-template](freemarker\word-template.png)



变量地方采用freemarker语法 ${} 来代替，变量名称需要跟具体实际情况一致





#### 2.2 word模板转xml文件



word模板制作完成后，使用word编辑器 另存为 xml文件

~~~
文件 -> 另存为 -> Word XML 文档(*.xml)
~~~



使用编辑器打开xml文件，查看 模板文件中定义的变量是否正确

有时候转成XML文件后，${变量名} 分开了，则需要手动填写在一起



如是还需要使用 freemarker的其他复杂的标签语法，则需要对XML文件内容进行修改

比如：本模拟实例中，课程成绩是一个数组，则需要去进行修改XML文件内容，采用freemarker的 数组遍历语法 <#list>



~~~xml
<!-- 
针对课程成绩是多个，需要修改XML内容文件
使用freemarker的遍历数组的语法 <#list >
-->
<#list courses as c>
    
<w:tr>
	<w:tblPrEx>
		<w:tblBorders>
			<w:top w:val="single" w:color="auto" w:sz="4" w:space="0" />
			<w:left w:val="single" w:color="auto" w:sz="4"
				w:space="0" />
			<w:bottom w:val="single" w:color="auto" w:sz="4"
				w:space="0" />
			<w:right w:val="single" w:color="auto" w:sz="4"
				w:space="0" />
			<w:insideH w:val="single" w:color="auto" w:sz="4"
				w:space="0" />
			<w:insideV w:val="single" w:color="auto" w:sz="4"
				w:space="0" />
		</w:tblBorders>
		<w:tblCellMar>
			<w:left w:w="108" w:type="dxa" />
			<w:right w:w="108" w:type="dxa" />
		</w:tblCellMar>
	</w:tblPrEx>
	<w:tc>
		<w:tcPr>
			<w:tcW w:w="2129" w:type="dxa" />
		</w:tcPr>
		<w:p>
			<w:pPr>
				<w:jc w:val="center" />
				<w:rPr>
					<w:rFonts w:hint="default" />
					<w:b />
					<w:bCs />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
			</w:pPr>
			<w:r>
				<w:rPr>
					<w:rFonts w:hint="eastAsia" />
					<w:b />
					<w:bCs />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
				<w:t>课程名称</w:t>
			</w:r>
		</w:p>
	</w:tc>
	<w:tc>
		<w:tcPr>
			<w:tcW w:w="2130" w:type="dxa" />
		</w:tcPr>
		<w:p>
			<w:pPr>
				<w:rPr>
					<w:rFonts w:hint="default" />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
			</w:pPr>
			<w:r>
				<w:rPr>
					<w:rFonts w:hint="eastAsia" />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
				<w:t>${c.courseName}</w:t>
			</w:r>
		</w:p>
	</w:tc>
	<w:tc>
		<w:tcPr>
			<w:tcW w:w="2131" w:type="dxa" />
		</w:tcPr>
		<w:p>
			<w:pPr>
				<w:jc w:val="center" />
				<w:rPr>
					<w:rFonts w:hint="default" />
					<w:b />
					<w:bCs />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
			</w:pPr>
			<w:r>
				<w:rPr>
					<w:rFonts w:hint="eastAsia" />
					<w:b />
					<w:bCs />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
				<w:t>考试成绩</w:t>
			</w:r>
		</w:p>
	</w:tc>
	<w:tc>
		<w:tcPr>
			<w:tcW w:w="2132" w:type="dxa" />
		</w:tcPr>
		<w:p>
			<w:pPr>
				<w:rPr>
					<w:rFonts w:hint="default" />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
			</w:pPr>
			<w:r>
				<w:rPr>
					<w:rFonts w:hint="eastAsia" />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
				<w:t>${c.courseScore}</w:t>
			</w:r>
			<w:r>
				<w:rPr>
					<w:rFonts w:hint="default" />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
				<w:t></w:t>
			</w:r>
			<w:r>
				<w:rPr>
					<w:rFonts w:hint="eastAsia" />
					<w:vertAlign w:val="baseline" />
					<w:lang w:val="en-US" w:eastAsia="zh-CN" />
				</w:rPr>
				<w:t></w:t>
			</w:r>
		</w:p>
	</w:tc>
</w:tr>
    
</#list>

~~~





#### 2.3 XML文件转freemarker模板文件ftl



修改完xml文件后，需要转成freemarker模板文件，直接修改后缀成为 .ftl

如：student.xml --> student.ftl

放在存放freemarker模板文件夹下 resources/template/ftl



至此，freemarker模板已经制作完成了，接下来采用freemarker框架的Java API来读取word模板生成word文件



#### 2.4 编写工具类



工具类



第一步：先获取模板

~~~java
/**
 * @Description: 
 * 获取模板
 * @author: TianwYam
 * @date 2021年5月4日 上午9:36:13
 * @param templateName 模板名称
 * @return
 */
public static Template getTemplate(String templateName) {
	
	try {
		Configuration configuration = new Configuration(Configuration.VERSION_2_3_21);
		String dir = FreeMarkerUtils.class.getClassLoader().getResource("templates/ftl").getFile();
		LOG.info("模板目录：{}", dir);
		configuration.setDirectoryForTemplateLoading(new File(dir));
		configuration.setDefaultEncoding("UTF-8");
		configuration.setTemplateExceptionHandler(TemplateExceptionHandler.RETHROW_HANDLER);
		
		return configuration.getTemplate(templateName);
	} catch (Exception e) {
		LOG.error("获取模板发生异常", e);
	}
	
	return null ;
}

~~~



第二步：根据模板，填充数据，输出word文件

~~~java


/**
 * @Description: 
 *  输出word文件
 * @author: TianwYam
 * @date 2021年5月4日 上午9:41:37
 * @param wordOutputPath word文件输出的全路径
 * @param templateName 模板的名称
 * @param data 需要填充的数据
 */
public static void outputWord(String wordOutputPath, String templateName, Map<String, Object> data) {
	
	Template template = getTemplate(templateName);
	if (template != null) {
		try {
			template.process(data, new FileWriter(wordOutputPath));
			LOG.info("生成word文件成功");
		} catch (TemplateException | IOException e) {
			LOG.error("生成word文件失败", e);
		}
	}
	
}

~~~



#### 2.5 测试



此处模拟的数据，实际情况可以根据数据库、文件、网络等数据生成word文件

~~~java
public static void main(String[] args) {
	
	// 课程
	List<Courses> courses = new ArrayList<>();
	courses.add(Courses.builder().courseId(100).courseName("语文").courseScore(87).build());
	courses.add(Courses.builder().courseId(110).courseName("数学").courseScore(94).build());
	courses.add(Courses.builder().courseId(120).courseName("英语").courseScore(67).build());
	courses.add(Courses.builder().courseId(130).courseName("政治").courseScore(54).build());
	
	
	// 学生
	Student student = Student.builder()
			.id(2021050611)
			.name("花无缺")
			.age(25)
			.sex(1)
			.address("四川省成都市大熊猫基地养殖员")
			.height(175)
			.courses(courses)
			.build();
	
	// 生成word文件
	outputWord("E:\\javaWork\\freemarker\\student.docx", "student.ftl", JSON.parseObject(JSON.toJSONString(student)));
}
~~~



#### 2.6 生成的word文件效果



![word-template](freemarker\word-output.png)

输出结果看出，课程成绩是一个数组，需要进行循环遍历获取到

性别需要翻译，这儿可以使用freemarker的三目运算符来进行转换

~~~java
${ (sex == 0)?then('女', '男') }
~~~

结果：

![word-template](freemarker\word-output2.png)

