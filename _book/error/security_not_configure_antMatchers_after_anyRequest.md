## Can't configure antMatchers after anyRequest



### 报错现象

配置spring security时，报错如下：

~~~java

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'springSecurityFilterChain' defined in class path resource [org/springframework/security/config/annotation/web/configuration/WebSecurityConfiguration.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [javax.servlet.Filter]: Factory method 'springSecurityFilterChain' threw exception; nested exception is java.lang.IllegalStateException: Can't configure antMatchers after anyRequest
	at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:656) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:484) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(AbstractAutowireCapableBeanFactory.java:1338) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1177) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:557) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:517) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:323) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:222) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:321) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:310) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:879) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:878) ~[spring-context-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:550) ~[spring-context-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:141) ~[spring-boot-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:747) [spring-boot-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:397) [spring-boot-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:315) [spring-boot-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1226) [spring-boot-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1215) [spring-boot-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at com.tianya.springboot.security.SecurityApplication.main(SecurityApplication.java:16) [classes/:na]
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [javax.servlet.Filter]: Factory method 'springSecurityFilterChain' threw exception; nested exception is java.lang.IllegalStateException: Can't configure antMatchers after anyRequest
	at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:185) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:651) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	... 21 common frames omitted
Caused by: java.lang.IllegalStateException: Can't configure antMatchers after anyRequest
	at org.springframework.util.Assert.state(Assert.java:73) ~[spring-core-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.security.config.annotation.web.AbstractRequestMatcherRegistry.antMatchers(AbstractRequestMatcherRegistry.java:122) ~[spring-security-config-5.2.1.RELEASE.jar:5.2.1.RELEASE]
	at com.tianya.springboot.security.config.SecurityConfig.configure(SecurityConfig.java:22) ~[classes/:na]
	at org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter.getHttp(WebSecurityConfigurerAdapter.java:231) ~[spring-security-config-5.2.1.RELEASE.jar:5.2.1.RELEASE]
	at org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter.init(WebSecurityConfigurerAdapter.java:322) ~[spring-security-config-5.2.1.RELEASE.jar:5.2.1.RELEASE]
	at org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter.init(WebSecurityConfigurerAdapter.java:92) ~[spring-security-config-5.2.1.RELEASE.jar:5.2.1.RELEASE]
	at com.tianya.springboot.security.config.SecurityConfig$$EnhancerBySpringCGLIB$$bfbad510.init(<generated>) ~[classes/:na]
	at org.springframework.security.config.annotation.AbstractConfiguredSecurityBuilder.init(AbstractConfiguredSecurityBuilder.java:370) ~[spring-security-config-5.2.1.RELEASE.jar:5.2.1.RELEASE]
	at org.springframework.security.config.annotation.AbstractConfiguredSecurityBuilder.doBuild(AbstractConfiguredSecurityBuilder.java:324) ~[spring-security-config-5.2.1.RELEASE.jar:5.2.1.RELEASE]
	at org.springframework.security.config.annotation.AbstractSecurityBuilder.build(AbstractSecurityBuilder.java:41) ~[spring-security-config-5.2.1.RELEASE.jar:5.2.1.RELEASE]
	at org.springframework.security.config.annotation.web.configuration.WebSecurityConfiguration.springSecurityFilterChain(WebSecurityConfiguration.java:104) ~[spring-security-config-5.2.1.RELEASE.jar:5.2.1.RELEASE]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_151]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_151]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_151]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_151]
	at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:154) ~[spring-beans-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	... 22 common frames omitted


~~~



### 解决办法：

在 SecurityConfig 配置文件中 configure方法里去掉默认配置

~~~java
// 不能够重复配置，应该去掉
super.configure(http);
~~~



是由于配置错误，不仔细导致

~~~java

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter{

	
	/**
	 * 自定义配置权限 等操作
	 */
	@Override
	protected void configure(HttpSecurity http) throws Exception {
		
        // 不能够重复配置，应该去掉
		// super.configure(http);
        
		// 自定义配置
		http.authorizeRequests()
		.antMatchers("/test").permitAll()
		.antMatchers("/*").authenticated()
		.and()
		.formLogin();
	}
}

~~~

