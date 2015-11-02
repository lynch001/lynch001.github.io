---

layout: post
title: tomcat-redis-session-manager小坑记录
category: code

---

> 之前系统虽然使用了阿里云的负载均衡,但是每台服务器的session各自管理，就会出现session不同步的问题．

<!--more-->

解决方案

拓展tomcat的session模块，将session id存储在缓存中进行统一管理

因为目前项目是基于redis所以就选用了一个开源框架[tomcat-redis-session-manager](https://github.com/jcoleman/tomcat-redis-session-manager)
不过按照文档使用的过程中出现了lib不兼容的问题

尝试过后一下的包为稳定版本

jedis-2.2.1.jar

commons-pool-1.6.jar

tomcat-redis-session-manager-1.2-tomcat-7-java-7.jar

以上方案在tomcat 7.0.64中测试通过
