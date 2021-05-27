## Java中注解





### 使用语法



采用@符合 和 interface 添加到注解名称前面，跟定义接口类似



简单定义一个 名称叫 NotAuth 的注解

~~~java
public @interface NotAuth {
	// 内容 参数等
}
~~~

但是这个注解不完整，需要添加一些描述，称为 元注解



### 元注解



描述注解的注解称为元注解



Java内置的元注解有四种：

- @Target  目标，表示该注解运用到什么地方，作用到的目标类型(常用类型枚举：java.lang.annotation.ElementType )
- @Retention  表示该注解在什么级别保存，被保留的时间(常用类型枚举：java.lang.annotation.RetentionPolicy )
- @Documented  用于描述该注解是否可以生成到java-doc文档中
- @Inherited  描述允许子类继承父类的这个注解，如一个父类使用了这个注解，子类是默认继承了这个注解的



常用的目标枚举 @Target ：

- ElementType.TYPE 类上
- ElementType.METHOD 方法上
- ElementType.FIELD 属性上
- ElementType.PARAMETER 参数上



保留时间级别 @Retention：

- RetentionPolicy.RUNTIME   运行时阶段，整个VM运行时都会保留，因此可使用反射机制读取注解信息
- RetentionPolicy.SOURCE   源码阶段，在Java文件中存在，编译成CLASS文件时就被抛弃
- RetentionPolicy.CLASS   字节码阶段，编译成CLASS文件时存在，在VM运行时被抛弃





### 完整注解



~~~java
// 运行时
@Retention(RetentionPolicy.RUNTIME)
// 方法 和 类上 都可以添加这个注释
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface NotAuth {

}
~~~



### 反射API



Class、Method、Field等上面都可以使用API操作注解



java.lang.Class<T>

java.lang.reflect.Method

java.lang.reflect.Field



具体操作的API

~~~java

// 获取注解
public <A extends Annotation> A getAnnotation(Class<A> annotationClass);


// 判断是否有注解
boolean isAnnotationPresent(Class<? extends Annotation> annotationClass);

// 获取所有注解
Annotation[] getAnnotations();
~~~











