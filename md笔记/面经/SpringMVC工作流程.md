# SpringMVC工作流程

## 1、工作流程图

![img](http://img.reainyday.top/16de2afc42ca928d)

## 2、工作流程

1. 客户端发送请求到前端控制器DispatcherServlet；
2. DispatcherServlet收到请求后调用处理器映射器HandlerMapping；
3. 处理器映射器HandlerMapping找到具体的处理器，生成处理器对象及处理器拦截器（如果有则生成）一并返回到DispatcherServlet；
4. DispatcherServlet调用处理器适配器HandlerAdapter；
5. HandlerAdapter 经过适配调用具体的处理器(Controller,也叫后端控制器)方法；
6. 处理器执行完成返回ModelAndView给HandlerAdapter；
7. HandlerAdapter将ModelAndView对象返回到DispatcherServlet；
8. DispatcherServlet 将ModelAndView传给ViewReslover 视图解析器；
9. ViewReslover 解析后返回具体View给DispatcherServlet；
10. DispatcherServlet 调用视图解析器根据View进行渲染视图(即将模型数据填充至视图 中)；
11. 将渲染后的页面返回到前端控制器DispatcherServlet；
12. DispatcherServlet 将渲染后的页面响应到客户端。

## 3、组件

1. DispatcherServlet(前端控制器)：核心组件，协调调用其他组件。
2. HandlerMapping(映射处理器)：根据请求查找处理该请求的处理器(Handler)。
3. Handler:处理请求的方法。
4. HandlerAdapter(处理器适配器)：适配参数以及调用执行处理器方法。
5. ModelAndView:封装跳转的页面以及向页面传递的参数。
6. ViewResolver(视图解析器)：查找跳转的页面并将数据解析到该页面。

