---
title: SpringCloud零碎知识整理
date: 2018-08-26 10:09:03
tags: java spring
categories: java
---
#### 元注解
1. @Retention
```
用于提示注解被保留多长时间，有三种取值
1. SOURCE
    保留在源码级别，被编译器抛弃(@Override就是此类)
2. CLASS
    保留在编译后的类文件级别，但是被虚拟机丢弃；
3. RUNTIME
    保留至运行时，可以被反射读取
```
2. @Target

```
用于提示该注解使用的地方
1. TYPE 
    Class, interface (including annotation type), or enum declaration 
    类,接口，注解，enum
2. FIELD
    Field declaration (includes enum constants)
    属性域
3. METHOD
    Method declaration 
    方法
4. PARAMETER
    Formal parameter declaration 
    参数
5. CONSTRUCTOR
    Constructor declaration
    构造函数
6. LOCAL_VARIABLE
    Local variable declaration
    局部变量
7. ANNOTATION_TYPE
    Annotation type declaration
    注解类型
8. PACKAGE
    Package declaration
    包
9. TYPE_PARAMETER
    Type parameter declaration
10. TYPE_USE
    Use of a type
```
#### 常用注解
##### 被依赖的bean注入进入
1. @Override
```
使用了@Override注解的方法必须override父类或者java.lang.Object中的一个同名方法。
@Target(ElementType.METHOD)  只能使用在方法上
@Retention(RetentionPolicy.SOURCE) 被编译器处理，然后抛弃掉
```
2. @Autowired
```
autowire=byType 就是根据类型的自动注入依赖（基于注解的依赖注入），可以被使用再属性域，方法，构造函数上
```
3. @Qualifier

```
就是 autowire=byName, @Autowired注解判断多个bean类型相同时，就需要使用 @Qualifier("xxBean") 来指定依赖的bean的id
```
4. @Resource

```
属于JSR250标准，用于属性域额和方法上。也是 byName 类型的依赖注入。使用方式：@Resource(name="xxBean"). 不带参数的 @Resource 默认值类名首字母小写

JSR-330标准javax.inject.*中的注解(@Inject, @Named, @Qualifier, @Provider, @Scope, @Singleton)。@Inject就相当于@Autowired, @Named 就相当于 @Qualifier, 另外 @Named 用在类上还有 @Component的功能。
```
##### 将类注解成spring的bean工厂中一个一个的bean
5. @Component

```
把普通pojo实例化到spring容器中
```
6.  @Controller

```
用于标记在一个类上，使用它标记的类就是一个SpringMVC Controller对象。
单单使用@Controller 标记在一个类上还不能真正意义上的说它就是SpringMVC 的一个控制器类，因为这个时候Spring 还不认识它。那么要如何做Spring 才能认识它呢？
这个时候就需要我们把这个控制器类交给Spring 来管理。有两种方式：
1）在SpringMVC 的配置文件中定义MyController 的bean 对象
2）在SpringMVC 的配置文件中告诉Spring 该到哪里去找标记为@Controller 的Controller 控制器
```
7.  @Service

```
service层是业务逻辑层注解，这个注解只是标注该类处于业务逻辑层
```

8.  @Repository
```
可以标记在任何的类上，用来表明该类是用来执行与数据库相关的操作（即dao对象），并支持自动处理数据库操作产生的异常
```
9.  @RequestMapping

```
RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。
RequestMapping注解有六个属性，下面我们把她分成三类进行说明
1. value， method
    value :指定请求的实际地址，
    method:指定请求的method类型， GET、POST、PUT、DELETE等；
2. consumes，produces
    consumes:指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;
    produces:指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回
3. params，headers
    params :指定request中必须包含某些参数值是，才让该方法处理。
    headers:指定request中必须包含某些指定的header值，才能让该方法处理请求。

```

#### 自定义注解
结合自定义注解和AOP或者过滤器，是一种十分强大的武器
比如可以使用注解来实现权限的细粒度的控制——在类或者方法上使用权限注解，然后在AOP或者过滤器中进行拦截处理

```
/**
 * 不需要登录注解
 */
@Target({ ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface NoLogin {
}
```
我们自定义了一个注解 @NoLogin, 可以被用于 方法 和 类 上，注解一直保留到运行期，可以被反射读取到。
该注解的含义是：被 @NoLogin 注解的类或者方法，即使用户没有登录，也是可以访问的。下面就是对注解进行处理了：

```
/**
 * 检查登录拦截器
 * 如不需要检查登录可在方法或者controller上加上@NoLogin
 */
public class CheckLoginInterceptor implements HandlerInterceptor {
    private static final Logger logger = Logger.getLogger(CheckLoginInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                             Object handler) throws Exception {
        if (!(handler instanceof HandlerMethod)) {
            logger.warn("当前操作handler不为HandlerMethod=" + handler.getClass().getName() + ",req="
                        + request.getQueryString());
            return true;
        }
        HandlerMethod handlerMethod = (HandlerMethod) handler;
        String methodName = handlerMethod.getMethod().getName();
        // 判断是否需要检查登录
        NoLogin noLogin = handlerMethod.getMethod().getAnnotation(NoLogin.class);
        if (null != noLogin) {
            if (logger.isDebugEnabled()) {
                logger.debug("当前操作methodName=" + methodName + "不需要检查登录情况");
            }
            return true;
        }
        noLogin = handlerMethod.getMethod().getDeclaringClass().getAnnotation(NoLogin.class);
        if (null != noLogin) {
            if (logger.isDebugEnabled()) {
                logger.debug("当前操作methodName=" + methodName + "不需要检查登录情况");
            }
            return true;
        }
        if (null == request.getSession().getAttribute(CommonConstants.SESSION_KEY_USER)) {
            logger.warn("当前操作" + methodName + "用户未登录,ip=" + request.getRemoteAddr());
            response.getWriter().write(JsonConvertor.convertFailResult(ErrorCodeEnum.NOT_LOGIN).toString()); // 返回错误信息
            return false;
        }
        return true;
    }
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response,
                           Object handler, ModelAndView modelAndView) throws Exception {
    }
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
                                Object handler, Exception ex) throws Exception {
    }
}
```
