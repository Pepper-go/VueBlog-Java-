# shiro



## Subject对象

Subject可以理解为一个用户，当前用户

#### 获取

- Subject subject = SecurityUtils.getSubject();
-  从shiro管理的session中取出对象User user = (User) SecurityUtils.getSubject().getPrincipal()

#### 作用

获取Session对象，这是shiro提供的session而不是httpsession，不依赖http服务器，整合http服务器会自动实现HttpServletSession

###### 登录

![image-20200713091014748](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200713091014748.png)

###### 得到当前登录用户名

![image-20200713091046141](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200713091046141.png)

###### 校验当前用户权限

![image-20200713091119907](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200713091119907.png)

###### 退出登录

![image-20200713091141800](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200713091141800.png)





## session管理

- Shiro提供了完整的企业级会话管理功能，不依赖于底层容器
- 直接使用 Shiro 的会话管理可以直接替换如 Web 容器的会话管理
  - 因此，shiro框架的存在直接替换掉了Web容器会话管理
  - 关闭掉本地shiro session，而引入整合shiro+redis,将会话缓存在redis可以方便实现会话共享
  - shiro除了作为登录验证授权框架外还作为分布式部署时会话共享的桥梁



#### Shiro会话特性

- 基于pojo
- shiro中的session可以很容易的集成第三方的缓存产品完成集群的功能
- 单点登录的支持：shiro session基于普通java对象，使得它更容易存储和共享，可以实现跨应用程序共享。可以根据共享的会话，来保证认证状态到另一个程序。从而实现单点登录





#### 会话相关API

- Subject subject = SecurityUtils.getSubject()
- Session session = subject.getSession()



#### 会话管理器

![image-20200713093516401](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200713093516401.png)







#### shiro session/redis

- session可以存在服务器的内存，数据库等等；
- 使用redis存储缓存信息时，使用了Shiro的Native Session Manager替代了Tomcat本身的Session Manager，并且用开源插件为Shiro的Native Session Manager配置了redis的SessionDAO，这个插件实现了cacheManager，sessionManager，redisSessionDAO
- 所有的session会话都缓存在redis中，对session的CRUD都是操作redis完成，redis进行集群时，session也会达到集群效果
- 这就达到了外置session的作用

