# 前端Vue开发

前端Vue技术栈主要为Vue+ElementUI+Vue router+Vuex+axios+webpack



## 环境搭建

- 搭建node.js
- 安装vue，创建导出项目后导入IDEA开发
- 安装elementui+axios，集成axios（在main.js下引入axios依赖）



## 页面路由

#### 定义页面

/views 目录下定义 Login.vue/Blogs.vue/BlogDetail.vue/BlogEdit.vue

#### 配置路由中心

/routers/index.js中定义页面路由（请求路径与跳转的页面关系）

#### 页面开发

页面的开发是在先前定义的.vue中开发的，在<template>中定义组件；<script>中数据绑定，数据校验，定义控件绑定事件的方法，响应数据存取判断弹窗路由等等;<style> 做样式调整

- 公共组件Header开发，在/src新建conponents目录，新建一个Header.vue，展示信息，提供一些页面路由按钮

- Login.vue ： 表单，提交/置位按钮；表单数据校验，提交表单，ajax提交请求，取出响应数据jwt和userinfo共享到/store/index.js,响应，路由博客详情页；否则弹窗错误提示
- Blogs.vue：引入代表的Header标签，时间线组件，卡片组件做分页展示；<script>中import Header，共享数据，初始化分页参数，定义了分页方法并且在生命周期函数created（）调用
- BlogsEdit.vue：三个文本框输入标题摘要，引入markdown插件输入文章内容，以及两个创建，重置按钮，<script>内做组件的数据共享，输入提示；按钮事件绑定方法定义，发送ajax请求，根据响应数据弹窗显示；生命周期函数created（）拿到动态路由的blogid，发送ajax请求查询对应blog，有则显示在页面上，无则显示空在页面上
- BlogsDetail.vue: 博客详情内容用markdown-it进行渲染，一个编辑文字链接路由到blogEdit；<script>组件内共享数据，生命周期函数，拿到动态路由参数id后ajax请求再将返回数据markdown-it渲染到elementui组件上

## axios全局拦截

- 用axios做一个后置拦截，对于响应数据的code和status不正常时弹窗提示



- /src目里下创建axios.js。定义axios的拦截并在main.js中引入

## 路由权限拦截

- 前后端分离项目，后端只能限制接口权限，前台页面显示仍需要控制

- /src定义permission.js，拦截所有具有元属性meta.requireAuth的路由需要进行token验证，不存在token则跳转到登录界面
- 在定义页面路由时，在meta信息进行定义，控制除了登录外的所有路由都设置需要验证token的
- 在main.js中import permission.js即可