### 流程
1. 用户发送请求到前端控制器(DispatcherServlet)
2. 前端控制器请求HandlerMapping查找Handler
   （可以根据xml配置、注解进行查找）
3. 处理器映射器HandlerMapping向前端控制器返回Handler
4. 前端控制器调用处理器适配器去执行Handler
5. 处理器适配器执行Handler
6. Handler执行完成给适配器返回ModelAndView 
    (ModelAndView是SpingMVC框架的一个底层对象，包括了Model和View)
7. 处理器适配器向前端控制器返回ModelAndView
8. 前端控制器请求视图解析器进行视图解析
    （根据逻辑视图名解析成真正的视图jsp）
9. 视图解析器向前端控制器返回View
10. 前端控制器进行视图渲染
    （视图渲染将模型数据填充到request域中，其中模型数据在ModelAndView对象中）
11. 前端控制器向用户响应结果

### 组件
1. 前端控制器DispatcherServlet
    作用：接收请求，响应结果，相当于转发器
2. 处理器映射器HandlerMapping
    作用：根据请求的url查找Handler
3. 处理器适配器HandlerAdapter
    作用：按照特定的规则去执行Handler
4. 处理器Handler（开发）
4. 视图解析器View resolve
    作用：进行视图解析，根据逻辑视图名解析成真正的试图view
5. 视图view（开发）
    作用：view是一个接口，实现类支持不同的view类型（jsp、pdf...）


