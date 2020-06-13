# 个人简历

## 基础信息

姓名：####  
户籍/出生地：上海  
邮箱：eXVraWxvaEBsaXZlLmNu  
手机: MTczMTcxOTEzOTg=  

## 求职意向

Java开发工程师

## 专业技能

- 开发语言:  
  主Java,副Javascript[(查看)](https://gitee.com/ashin10/javascript-basic)  ,了解Golang[(查看)](https://github.com/yukiloh/golang-basic-project)  

- 框架运用:  
  SpringBoot[(查看)](https://github.com/yukiloh/spring-boot-basic-project) ,   SpringCloud[(查看)](https://github.com/yukiloh/springboot-project-for-community) ,   Vue[(查看)](https://github.com/yukiloh/my-vue-shop-project)  
  Mybatis , Hibernate/Spring Data JPA[(查看)](https://github.com/yukiloh/jpa-project)  

- 其他补充:  
  Linux,Docker,Nginx,Git  
  日语Native  

## 个人简介

对于技术,本人属于需求驱动型,发现问题后习惯于去了解背后原理  
了解过Java的底层[(查看)](https://github.com/yukiloh/one-line-memo) , SpringBoot启动源码[(查看)](https://www.jianshu.com/p/638508cab977) , B-Tree[(查看)](https://www.jianshu.com/p/4dcfd7085a85) , 23种设计模式[(查看)](https://www.jianshu.com/p/3f9e289cf51c)
勤于提高自身业务能力,通过LeetCode , SQLZOO[(查看)](https://www.jianshu.com/p/087489568f2a)等题库来完善自身知识储备  
富有Geek精神~~爱折腾~~,善于运用实践所学,例如:  
maria安全一瞥[(查看)](https://www.jianshu.com/p/558332f97a7d) ,  和idea说再见时[(查看)](https://www.bilibili.com/video/BV1Dz411B7J7) 等  

## 项目经历

### 电商后台CRM

- 展示地址:  [https://murasakichigo.xyz/tomorrow/#/my-shop/login](https://murasakichigo.xyz/tomorrow/#/my-shop/login)  (境外服务器,访问可能受限)  
  备选地址: http://34.85.51.72:8848/

- 项目概述:  前后端分离的电商后台管理平台.允许进行用户,权限,商品,订单的各种操作
  ![项目结构图](https://raw.githubusercontent.com/yukiloh/my-image-repo/master/%E6%97%A0%E6%A0%87%E9%A2%98.jpg)
- 技术栈:  前端Vue+ElementUI,后端SpringBoot+JPA,数据库MariaDB,前端服务器Nginx  

- 其他补充:  
  - 采用JPA而不是Mybatis主要考虑了项目的规格以及开发效率.JPA的应用中侧重于命名查询,如果复杂查询则通过NativeQuery=true来实现
  - 数据库没有采用外键,全手动维护.例如用户-角色-拥有权限类型-权限属性全部分表构建,表结构复杂,测试案例中使用外键极易造成hibernate崩溃  
  - 没有采用Express是因为更熟悉Nginx,加上服务器的其他项目也在使用Nginx,避免资源浪费

---

### 问答社区  

- 项目概述:  仿Elastic中文社区的一个问答社区项目.访问者可以通过GitHub账号(项目中接入其oAuth登陆)或注册新账号,来进行参与社区的问题讨论(发起问题,回复问题等)
- 技术栈:  SpringBoot,Mybatis,Redis,Shiro,模板引擎Thymeleaf,页面框架Bootstrap,数据库MariaDB
![预览效果](https://raw.githubusercontent.com/yukiloh/my-image-repo/master/%E6%9C%AA%E6%A0%87%E9%A2%98-1.png)
- 其他补充:  
  - 通过redis实现缓存问题数据,和对访问ip进行了次数限制
  - 权限认证采用Shiro,通过spring-aop实现全注解配置(@RequiresAuthentication等),免去了繁琐的xml配置  
    除少数全局拦截,其余通过filterChainDefinitionMap中的配置都转交为注解,提升开发效率以及代码易读性
  - 项目源码: [https://github.com/yukiloh/springboot-project-for-community](https://github.com/yukiloh/springboot-project-for-community)

---

### 个人服务器信息监测平台  

- 项目概述:  为了方便监控多台服务器的状态(流量使用,负载等)而设立的站点
  ![欢迎页面预览](https://raw.githubusercontent.com/yukiloh/my-image-repo/master/QQ%E6%88%AA%E5%9B%BE20200604210354.png)
- 技术栈:  
  - 前端: Vue(基础+axios),页面使用AdminLET模板
  - 后端: SpringBoot,Mybatis及配套工具,Shiro,Thymeleaf,数据库MariaDB

- 其他补充:  
  - 通过服务器运营商提供的api获取服务器当前信息,再将数据加密后通过rpc通讯(使用SpringBoot的`RestTemplate`),定时向站点后台发送服务器当前信息(SpringBoot整合的Quartz功能)
  - 考虑mybatis使用上的繁琐,因此项目内使用了mybatis-generator和通用mapper来提升开发效率[(了解详细)](https://github.com/yukiloh/spring-boot-basic-project/tree/master/SpringBoot_Project_05_Easy_Mybatis)
  - 页面使用了AdminLET提供的模板,通过thymeleaf的`th:insert`在页面内进行拼接(类似于Vue的Component的效果),同时使用iframe标签实现无刷换页
  
  - 为了方便朋友,接入过支付宝登陆
  ![登陆页面](https://raw.githubusercontent.com/yukiloh/my-image-repo/master/QQ%E6%88%AA%E5%9B%BE20200604205516.png)
  
---

## 在校经历

- 2009~2011 上海戏剧学院附属高中  
- 2011~2013 东京美罗斯语言学校  
- 2013~2017 东京农业大学  
