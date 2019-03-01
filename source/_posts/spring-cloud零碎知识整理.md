---
title: SpringCloud零碎知识整理
date: 2018-08-26 10:09:03
tags: java spring-cloud
categories: spring-cloud
---

```
1. profiles
    1.1 解决不同环境所需的不同变量、配置
    1.2 profile 一般可以在三个地方：settings.xml，pom.xml，profiles.xml()这个不常用）
    1.3 profile 是可以有多个的，也可以同时激活多个 profile，方便自由组合.
    1.4 小财迷的系统有local,dev,test,uat,prod,prod2这个几个环境变量配置,以及私服地址nexus
2. 多环境property文件的加载
    1. application.properties 是默认配置文件,不管指定什么profile,这个文件都会被打包
    2. application-{profile}.properties,指定环境的配置文件
3. Eureca服务端
    3.1 注解
    @EnableEurekaServer 将一个普通的Spring Boot应用变成了Eureka的注册中心
    @SpringBootApplication  表明这是一个springboot应用
    3.2 配置文件
    server.port: 8761   当前应用的端口号
    eureka.instance.hostname: localhost 应用的主机名称
    eureka.client.registerWithEureka: false 值为false意味着自己无须注册自己
    eureka.client.fetchRegistry: false  自己无须注册自己
    eureka.client.serviceUrl.defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/   设置与Eureka Server交互的地址，查询服务和注册服务都需要依赖这个地址。默认是http://localhost:8761/eureka ；多个地址可使用 , 分隔。
4. Zuul 
    4.1 注解
    @EnableZuulProxy        指明这是一个路由网关
    @EnableEurekaClient     指明这是一个Eureka客户端
    @SpringBootApplication  指明这是一个spring boot应用
    4.2 
    eureka.client.serviceUrl.defaultZone=http://172.19.189.210:8761/eureka/    指定Eureca注册中心
    eureka.instance.instance-id=${spring.cloud.client.ipAddress}:${server.port} 指定当前实例的地址以及端口
    spring.application.name 指定当前服务的名称
5. spring boot常用注解
    @SpringBootApplication 将项目设置为springboot项目
    @EnableTransactionManagement 开启事务配置相当于 xml的<tx:annotation-driven />
    @ServletComponentScan 使该注解后，Servlet、Filter、Listener 可以直接通过 @WebServlet、@WebFilter、@WebListener 注解自动注册，无需其他代码
    @EnableSwagger2 启用swagger
    @EnableWebMvc 在使用该注解后配置一个继承于WebMvcConfigurerAdapter的配置类即可配置好Spring Webmvc。
    @EnableAsync 开启异步执行的功能，这可以放在springboot主类上，或者放在我们的配置类上
    @EnableScheduling 开启定时调度的功能
    @EnableEurekaClien 基于spring-cloud-netflix,注册中心是eureka，那么就推荐@EnableEurekaClient
    @EnableDiscoveryClient  基于spring-cloud-commons.如果是其他的注册中心，那么推荐使用@EnableDiscoveryClient。
    @EnableFeignClients 启用feign进行远程调用
    @ComponentScan 告诉Spring 哪个packages 的用注解标识的类 会被spring自动扫描并且装入bean容器
6. 特定接口登录拦截
    6.1 自定义注解
        @Documented
        @Inherited
        @Target({ElementType.METHOD,ElementType.TYPE})
        @Retention(RetentionPolicy.RUNTIME)
        public @interface Auth4Admin {
             boolean validate() default true;
        }
    6.2 在需要拦截的接口上加此注解
        @Auth4Admin
        @PostMapping("/query/my")
        public BaseResult myInfo(HttpSession session) {
            return new BaseResult((SysUserModel) session.getAttribute("sysUser"));
        }
    6.3 配置拦截器实现类
        if(handler.getClass().isAssignableFrom(HandlerMethod.class)){
            Auth4Admin authPassport = ((HandlerMethod) handler).getMethodAnnotation(Auth4Admin.class);
            if(authPassport != null){
                ...
            }
            return ture;
    6.4 配置拦截器
        @Configuration
        public class CustomMVCConfig extends WebMvcConfigurationSupport {
         @Override
         public void addInterceptors(InterceptorRegistry registry) {
              registry.addInterceptor(authInterceptor()).addPathPatterns("/**");
            registry.addInterceptor(auth4AdminInterceptor()).addPathPatterns("/**");
        }
        @Bean
        Auth4AdminInterceptor auth4AdminInterceptor(){
          return new Auth4AdminInterceptor();
        }
7. spring boot集成swagger
    7.1 引入依赖
        <!-- Swagger -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.1</version>
        </dependency>

        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.1</version>
        </dependency>
    7.2 创建Swagger2配置类
    @Configuration
    public class Swagger2 {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.xiaocaimi.admin.web.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("springboot利用swagger构建api文档")
                .description("简单优雅的restfun风格，http://blog.csdn.net/saytime")
                .termsOfServiceUrl("http://blog.csdn.net/saytime")
                .version("1.0")
                .build();
    }
    }
    7.3 在Application类顶部添加注解 @EnableSwagger2 
    7.4 在Controller类中编写接口注解内容
        7.4.1 添加类注解
        @Api(value="/test", tags="Swagger测试接口模块")
        7.4.2 添加请求方法注解
        @ApiOperation(value="根据Id查询用户信息", notes = "根据Id查询用户信息,这里是详细说明")
        @ApiImplicitParam(name="id", value="用户唯一id", required = true, dataType = "Long")

```
