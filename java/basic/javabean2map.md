## Java bean 转 Map



<br/>

<br/>



项目中经常遇到需要实现 Java bean转Map，比如：调用第三方接口传参等



<br/>



总结了几种方式实现：

- apache commons-beanutils 方式
- fastjson 方式
- spring cglib 方式
- java 内省机制 的 方式





<br/><br/>



### 1、Apache commons-beanutils 方式





<br/>



maven 引入依赖：

~~~xml
<dependency>
	<groupId>commons-beanutils</groupId>
	<artifactId>commons-beanutils</artifactId>
	<version>1.9.4</version>
</dependency>
~~~





<br/>



工具类实现：

~~~java


/**
 * @description 对象转成Map (apache commons-beanutils 方式)
 * @author TianwYam
 * @date 2021年6月28日下午5:25:42
 * @param bean 实例对象
 * @return Map<String, String>
 */
public static Map<String, String> bean2Map(Object bean) {

	try {
		return BeanUtils.describe(bean);
	} catch (Exception e) {
		e.printStackTrace();
	}

	return new HashMap<>();
}

~~~



<br/>



这种方式有点不方便的就是 只能转成 Map&lt;String, String&gt;  不能转成其他的





<br/><br/>



### 2、Fastjson 方式



<br/>



maven引入依赖：

~~~xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>fastjson</artifactId>
	<version>1.2.73</version>
</dependency>
~~~



<br/>



工具类实现：

~~~java
/**
 * @description 对象转Map (fastjson 方式)
 * @author TianwYam
 * @date 2021年6月28日下午5:27:46
 * @param bean
 * @return
 */
public static Map<String, Object> fastJsonBean2Map(Object bean) {

	return JSON.parseObject(JSON.toJSONString(bean), 
			new TypeReference<Map<String, Object>>() {
	});
}

~~~



<br/>

fastjson方式很方便，很灵活

<br/><br/>



### 3、Spring Cglib 方式



<br/>



maven 引入依赖：

~~~xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-core</artifactId>
	<version>5.3.8</version>
</dependency>

~~~



<br/>



工具类实现：

~~~java

/**
 * @description 对象转Map (spring cglib 方式)
 * @author TianwYam
 * @date 2021年6月29日上午10:52:02
 * @param bean
 * @return
 */
@SuppressWarnings("all")
public static Map<String, Object> springBean2Map(Object bean) {

	Map<String, Object> map = new HashMap<>();
	BeanMap beanMap = BeanMap.create(bean);
	for (Object object : beanMap.entrySet()) {
		if (object instanceof Map.Entry) {
			Entry<String , Object> entry = (Entry<String, Object>)object ;
			String key = entry.getKey();
			map.put(key, beanMap.get(key));
		}
	}

	return map;
}
~~~





<br/><br/>





### 4、Java 内省机制 的 方式



<br/>



JDK自带就能实现，通过反射实现的



<br/>



~~~java

/**
 * @description
 *	对象转Map (java 内省机制 的 方式)
 * @author TianwYam
 * @date 2021年6月29日上午10:59:20
 * @param bean
 * @return
 */
public static Map<String, Object> javaBean2Map(Object bean)  {
	
	Map<String, Object> map = new HashMap<>();
	
	try {
		
		// 获取JavaBean的描述器
		BeanInfo b = Introspector.getBeanInfo(bean.getClass(), Object.class);
		
		// 获取属性描述器
		PropertyDescriptor[] pds = b.getPropertyDescriptors();
		
		// 对属性迭代
		for (PropertyDescriptor pd : pds) {
			
			// 属性名称
			String propertyName = pd.getName();
			
			// 属性值,用getter方法获取
			Method m = pd.getReadMethod();
			
			// 用对象执行getter方法获得属性值
			Object properValue = m.invoke(bean);
			
			// 把属性名-属性值 存到Map中
			map.put(propertyName, properValue);
		}
		
	} catch (Exception e) {
		e.printStackTrace();
	}
	
	return map;
}

~~~







