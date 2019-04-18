




写完 [springboot_crud_restful](<https://github.com/orrrz/springboot_crud_restful>) 这个 demo 后，进一步了解到 SpringMVC 对于统一异常处理的方法。

在这里我只写常用的两种：

1）实现 HandlerExceptionResolver 接口

2）使用@ControllerAdvice + @ExceptionHandler 注解

<br/>

## 1. 实现 HandlerExceptionResolver 接口

Spring MVC 提供了异常解析器 HandlerExceptionResolver 接口，将处理器( handler )执行时发生的异常，解析( 转换 )成对应的 ModelAndView 结果。源码如下：

```java
// HandlerExceptionResolver.java
public interface HandlerExceptionResolver {
    
    // 解析异常，转换成对应的 ModelAndView 结果
    @Nullable
    ModelAndView resolveException(
            HttpServletRequest request, HttpServletResponse response, @Nullable Object handler, Exception ex);
}
```



SpringMVC 处理请求出现异常时，会找到容器中 HandlerExceptionResolver 类型的对象，即实现 HandlerExceptionResolver 的类。换句话说，HandlerExceptionResolver 可以实现全局的异常控制，所有的 controller 发生的异常都会进入其实现类，需要做通用处理。

自定义异常处理类，实现接口 HandlerExceptionResolver，需要确保此类被扫描并装在到 Spring 容器，代码如下：

```java
@Component
public class CustomerExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        //1. 打印异常信息
        ex.printStackTrace();

        //2. 保存错误信息、跳转到错误页面！
        ModelAndView modelAndView = new ModelAndView();
        // 保存错误信息
        modelAndView.addObject("error", "系统繁忙"+ex.getMessage());
        // 设置错误页面
        modelAndView.setViewName("error");

        return modelAndView;
    }
}
```



- 访问控制器方法，可以捕获后台的所有异常，如果发现异常就会经过上面自定义的CustomerExceptionResolver类，并返回modelAndView。
- 可以创建多个自定义异常处理类，但是只会走一个，哪一个先加入到容器就先执行，其他的自定义异常处理类无效。



<br/>

## 2. 使用@ControllerAdvice + @ExceptionHandler 注解

@ControllerAdvice 注解标注当前 bean 是对 Controller 进行增强，具体增强什么，需要配合@ExceptionHandler 实现对异常的精确处理。

```
/**
 * Created by icejam.
 * 全局异常处理类
 */
@ControllerAdvice
public class BaseExceptionHandler {

    // 自定义异常
    @ExceptionHandler(SystemException.class)
    @ResponseBody
    public Result customHandler(SystemException e) {
        return new Result(false, StatusCode.ERROR, "系统异常");
    }

    // 其他未处理的异常，即通用处理
    @ExceptionHandler(Exception.class)
    @ResponseBody
    public Result error(Exception e) {
        return new Result(false, StatusCode.ERROR, e.getMessage());
    }
    
    // 如果 @ExceptionHandler 注解中未声明要处理的异常类型，则默认为参数列表中的异常类型，参数对象是controller层抛出的异常对象。
    @ExceptionHandler()
    @ResponseBody
    String handleException(Exception e){
        return e.getMessage();
    }
}
```

<br/>

{{% admonition tip tip %}}

第二种种方式更加灵活，推荐使用

具体案例可以参考我的[springboot_crud_restful](<https://github.com/orrrz/springboot_crud_restful>) Demo，里面对于全局异常的捕获与处理就是通过这个方式。

{{% /admonition %}}