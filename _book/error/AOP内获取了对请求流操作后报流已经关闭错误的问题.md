### 解决在AOP内获取了对请求流操作后报流已经关闭错误的问题



#### 问题现象

现象：在AOP中，获取了请求参数，使用了请求流体内容后，报错：java.io.IOException: Stream closed

~~~java

java.io.IOException: Stream closed
	at org.apache.catalina.connector.InputBuffer.read(InputBuffer.java:359) ~[tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.connector.CoyoteInputStream.read(CoyoteInputStream.java:132) ~[tomcat-embed-core-9.0.30.jar:9.0.30]
	at sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284) ~[na:1.8.0_92]
	at sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326) ~[na:1.8.0_92]
	at sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178) ~[na:1.8.0_92]
	at java.io.InputStreamReader.read(InputStreamReader.java:184) ~[na:1.8.0_92]
	at java.io.Reader.read(Reader.java:140) ~[na:1.8.0_92]
	at org.apache.commons.io.IOUtils.copyLarge(IOUtils.java:2537) ~[commons-io-2.6.jar:2.6]
	at org.apache.commons.io.IOUtils.copyLarge(IOUtils.java:2516) ~[commons-io-2.6.jar:2.6]
	at org.apache.commons.io.IOUtils.copy(IOUtils.java:2493) ~[commons-io-2.6.jar:2.6]
	at org.apache.commons.io.IOUtils.copy(IOUtils.java:2441) ~[commons-io-2.6.jar:2.6]
	at org.apache.commons.io.IOUtils.toString(IOUtils.java:1084) ~[commons-io-2.6.jar:2.6]
	at com.openapi.utils.AspectUtils.getRequestParams4body(AspectUtils.java:166) [classes/:na]
	at com.openapi.utils.AspectUtils.getRequestParams(AspectUtils.java:113) [classes/:na]
	at com.openapi.aop.OpenApiUserAuthAop.openApiVisitRecord(OpenApiUserAuthAop.java:187) [classes/:na]
	at com.openapi.aop.OpenApiUserAuthAop.auth(OpenApiUserAuthAop.java:151) [classes/:na]
	at com.openapi.aop.OpenApiUserAuthAop.userAuth(OpenApiUserAuthAop.java:91) [classes/:na]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_92]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_92]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_92]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_92]
	at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethodWithGivenArgs(AbstractAspectJAdvice.java:644) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethod(AbstractAspectJAdvice.java:633) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.aop.aspectj.AspectJAroundAdvice.invoke(AspectJAroundAdvice.java:70) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:747) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:95) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:747) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:689) [spring-aop-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at com.openapi.controller.StatisController$$EnhancerBySpringCGLIB$$6cebe8eb.statisBySate(<generated>) [classes/:na]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_92]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_92]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_92]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_92]
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:190) [spring-web-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:138) [spring-web-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:106) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:888) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:793) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1040) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:943) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:909) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:660) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883) [spring-webmvc-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:741) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53) [tomcat-embed-websocket-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at com.github.xiaoymin.swaggerbootstrapui.filter.SecurityBasicAuthFilter.doFilter(SecurityBasicAuthFilter.java:81) [swagger-bootstrap-ui-1.9.6.jar:na]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at com.github.xiaoymin.swaggerbootstrapui.filter.ProductionSecurityFilter.doFilter(ProductionSecurityFilter.java:53) [swagger-bootstrap-ui-1.9.6.jar:na]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at com.alibaba.druid.support.http.WebStatFilter.doFilter(WebStatFilter.java:123) [druid-1.1.10.jar:1.1.10]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201) [spring-web-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119) [spring-web-5.2.3.RELEASE.jar:5.2.3.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:541) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:139) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:367) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:860) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1598) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142) [na:1.8.0_92]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617) [na:1.8.0_92]
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61) [tomcat-embed-core-9.0.30.jar:9.0.30]
	at java.lang.Thread.run(Thread.java:745) [na:1.8.0_92]

~~~



#### 解决办法

解决思路：是由于request请求流内容是一次性消费，使用后会被关闭，所以需要重新对request请求进行包装，对请求体内容保存，每一次使用时，直接重新取保存好的内容体



第一步：自定义request请求包装类

~~~java

/**
 * @description
 *	自定义一个对request请求包装类
 * @author TianwYam
 * @date 2021年4月26日
 */
public class RequestReaderHttpServletRequestWrapper extends HttpServletRequestWrapper{

	// 保存请求体内容
    private final byte[] body ;
    
    public RequestReaderHttpServletRequestWrapper(HttpServletRequest request) throws IOException {
        super(request);
        // 取请求体内的 内容
        body = RequestUtils.getBodyStream(request);
    }
    
    @Override
    public BufferedReader getReader() throws IOException {
        return new BufferedReader(new InputStreamReader(getInputStream())) ;
    }
    
    @Override
    public ServletInputStream getInputStream() throws IOException {
        
    	// 每一次获取请求流时的内容都是保存好的
        final ByteArrayInputStream bais = new ByteArrayInputStream(body);
        
        return new ServletInputStream() {
            
            @Override
            public int read() throws IOException {
                return bais.read();
            }
            
            @Override
            public void setReadListener(ReadListener readListener) {
            	
            }
            
            @Override
            public boolean isReady() {
                return false;
            }
            
            @Override
            public boolean isFinished() {
                return false;
            }
        };
    }

}
~~~



第二步：定义拦截器拦截所有请求，并对request请求进行包装

~~~java

/**
 * @description
 *	自定义拦截器 进行拦截
 * @author TianwYam
 * @date 2021年4月26日
 */
@Order(0)
@WebFilter(urlPatterns="/api/v1/openapi/*", filterName="CustomHttpServletRequestFilter")
public class HttpServletRequestFilter implements Filter {
    
	private static final Logger LOG = LoggerFactory.getLogger(HttpServletRequestFilter.class);


    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain)
            throws IOException, ServletException {
     
        
        RequestReaderHttpServletRequestWrapper requestWrapper = null ;
        if (request instanceof HttpServletRequest ) {
        	HttpServletRequest httpServletRequest = (HttpServletRequest)request;
        	// 对 request请求进行包装 RequestReaderHttpServletRequestWrapper(自定义的包装类)
            requestWrapper = new RequestReaderHttpServletRequestWrapper(httpServletRequest);
        }
        
        if (requestWrapper == null) {
            filterChain.doFilter(request, response);
        }else {
            filterChain.doFilter(requestWrapper, response);
        }
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    	LOG.info("CustomHttpServletRequestFilter init");
    }
    
    @Override
    public void destroy() {
    	LOG.info("CustomHttpServletRequestFilter destroy");
    }

}

~~~



