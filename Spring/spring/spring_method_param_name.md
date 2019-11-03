## 获取方法参数名称





核心类：

~~~java
org.springframework.core.LocalVariableTableParameterNameDiscoverer
~~~



实现接口：org.springframework.core.ParameterNameDiscoverer

~~~java


public interface ParameterNameDiscoverer {

	/**
	 * Return parameter names for this method,
	 * or {@code null} if they cannot be determined.
	 * @param method method to find parameter names for
	 * @return an array of parameter names if the names can be resolved,
	 * or {@code null} if they cannot
	 */
	@Nullable
	String[] getParameterNames(Method method);

	/**
	 * Return parameter names for this constructor,
	 * or {@code null} if they cannot be determined.
	 * @param ctor constructor to find parameter names for
	 * @return an array of parameter names if the names can be resolved,
	 * or {@code null} if they cannot
	 */
	@Nullable
	String[] getParameterNames(Constructor<?> ctor);

}
~~~





### 工具类实现



工具类：

~~~java

	/**
	* @Function: BusiServiceUtils.java
	* @Description: 获取方法的参数的名称
	* @param clazz 目标对象
	* @param methodName 方法名称
	* @param parameterTypes 参数类型
	* @return 返回参数名称
	* @version: v1.0.0
	* @author: tianwyam
	* @date: 2019年11月3日 下午12:18:05
	 */
	public static String[] getMethodParamName(Class<?> clazz, String methodName, Class<?>[] parameterTypes) {
		try {
			Method method = clazz.getDeclaredMethod(methodName, parameterTypes);
			LocalVariableTableParameterNameDiscoverer parameterNameDiscoverer = new LocalVariableTableParameterNameDiscoverer();
			return parameterNameDiscoverer.getParameterNames(method);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}
	
~~~



### 场景



当自定义编写一些框架，通过配置式获取方法，然后根据参数名称 去 参数集 拿对应的值，然后通过反射执行方法

~~~java
method.invoke(obj, args);
~~~





