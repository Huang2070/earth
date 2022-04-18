# spring-boot-starter-web
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
spring-boot-starter-web是web支持模块, 这个模块包含了很多web请求相关的jar包, 基本可以涵盖一个web应用, 即spring mvc应用所需的jar包了.

![avatar](/resource/spring-boot-starter.png)
看依赖图, 发现spring-boot-starter-web包含了核心的: 
* spring-boot-starter
* jackson
* spring-core
* spring-mvc
* spring-boot-starter-tomcat


而spring-boot-starter-tomcat包含了一个嵌入的tomcat应用, 包括: 
* core
* el
* logging
* websocket

# spring-boot-starter-test
测试模块，包括 JUnit、Hamcrest、Mockito。