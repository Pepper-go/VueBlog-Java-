# 博客项目

- 该博客系统采用前后端分离结构，前端由Vue+Element开发，后端框架采用SpringBoot+Mybatis+MySQL搭建，

- 该博客系统可以提供登录，博客列表分页展示，博客详情查询和博客编辑页面以及博客登录状态注销；这个项目是我再GitHub上挖掘的，为了提高开发效率引入了MybatisPlus，Lombok，为了扩展系统高可用和高性能对项目做了分布式部署并用Nginx做负载均衡，会话共享方案由Redis解决



## 前端Vue开发

前端技术栈主要由 Vue+ElementUI+Vue router+Vuex+axios+webpack

- Vue+elementui+axios可以十分方便的搭建SPA，本项目共搭建了登录，博客列表，博客详情，博客编辑4个界面
- 前端开发主要搭建了4个vue界面和一个重用的header组件显示每个界面头部，提供按钮进行页面跳转实现页面路由

- 登录页面提交登录数据，axios发送ajax请求后，验证响应数据jwt跳转到博客列表
- 博客列表由时间线组件做列表展示（card）和分页，
- 博客列表点击发表文章进入文章编辑页面，集成markdown插件编辑完成点击发送路由到博客列表
- 博客列表点击card可以路由到博客详情页面
- 并且axios全局拦截，对jason串中的status字段不正常判断则弹窗提示重新登录，权限不足等
- 路由拦截，如果jwt存在则继续执行逻辑否则路由到登录页面，除了登录外所有页面理由都需要登录





## 后端开发

后端技术栈主要由Springboot+MybatisPlus+shiro+lombok，redis+hibernate Validatior+jwt

- 快速搭建SpringBoot工程，引入devtools和lombok插件后，集成MybatisPlus

- 自定义Result类封装异步统一返回的结果（code标识成功与否，结果消息，结果数据）

- 整合shiro+redis，jwt+shiro身份验证，shiro+jwt+redis实现会话共享

- @ControllerAdvice注解一个全局异常处理类处理异常

- HibernateValidator做前端数据的实体校验

- 定义一个WebMvcConfigurer类解决跨域问题

- 登录接口开发，接收登录名密码生成jwt返回给前端，jwt为了延期放在响应头Authorization中

- 博客接口提供了博客列表分页查询，博客id详情查询和博客编辑三个方法

  

## 项目部署

见另一个md文档

## 开发优化/遇到难点

#### Shiro整合Redis+jwt

- 项目中对于接口的定义是比较简单的，用户接口和博客接口都是完成用户登录退出和博客查询编辑的动能，最复杂的部分是shiro整合jwt完成登录认证授权以及shiro整合redis进行缓存
- Shiro提供的SessionManager显式替换掉了tomcat的SessionManager，同时又提供了RedisSessionManager和cacheManager；便捷将shiro权限数据和会话存储在redis中以便后续会话共享
- 配置shiro配置类时，用RedissessionDao实现sessionManager，用自定义的realm，sessionManager，cacheManager实现shiro核心组件securityManager，其中关闭了shiro自带session登录方式，后面用户需要通过jwt登录，所以同时还需要配置shiro的过滤器执行链定义以及相应工厂对象，自定义的realm实现
- 还需要自定义JwtToken（转换token），Jwtfilter（生成JwtToken，拦截校验，登录异常处理以及前置拦截实现过滤器跨域）
- 用户登录成功后生成的jwt在http头中，访问shiro时还需要转换为用于shiro认证的JwtToken才行）



#### 跨域问题与会话共享

- 前后端项目，由于自定义了http头添加了Authorization字段导致了复杂请求即preflighted request，这样即使时GET/POST等请求前会先发送OPTIONS请求，但是这个请求是不会携带自定义Authorization字段（shiro的session），则无法通过OPTIONS请求无法通过shiro验证（401），因此在定义的过滤器中JwtFilter重写了一个preHandle前置拦截，将跨域时发送的OPTIONS直接返回正常状态
- 同时，配置了自定义跨域类CORSConfig实现接口WebMvcConfigurer做跨域处理
- 这是我在Github上挖掘的项目，对跨域的处理过于复杂，同时部署项目时他是将打包后的文件重新放进了后端打包的文件夹中，通过后端代码多加了一个controller返回前端界面，虽然避免了跨域但是前后端代码重新耦合在了一起只是达到了便捷开发的效果，
- 跨域问题的根本是浏览器的同源策略，即脚本只能访问相同协议/相同主机名/相同端口的资源，跨域要遵循CORS规范
- 因为服务器搭建集群实现负载均衡会话共享通过Nginx+Redis，Nginx解决跨域的常见方案
- 将前端项目webpack打包后将/dist目录下的静态资源和index.html部署在Nginx上，对于静态资源的访问就不需要再转发到服务器可由Nginx直接返回，实现动静分离提高了系统的运行效率；同时在Nginx上实现负载均衡，由于实现动静分离此时负载均衡配置时都不用再进行域名解析相关配置直接在上游服务器upstream中指定ip+port和权重weight即可，经过上述配置后已经完成了动静分离+负载均衡+跨域问题







