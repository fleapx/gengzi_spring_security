[toc]
spring security 相关配置

目标
了解与Spring session ，redis 的整合

参考：spring security 实战书籍 6.6 章节

集群会话
单机提供单服务只能存在于测试环境，正式环境部署工程，一般都是集群部署或者单机多服务部署。看下两者会话信息的不同：

单机单服务：通过一个服务中间件实例，例如tomcat，来提供服务。使用session来保持会话信息，session 信息被存储在内存中。

集群部署或者单机多服务：采用了多台服务器，或者多个tomcat提供服务，但是多台服务器或者多个tomcat ，session 是无法共享的。大概有三种解决方案：

session 保持
session 复制
session 共享
session 保持
一般依靠负载均衡组件（例如nginx）ip哈希负载策略来将相同客户端的请求转发到同一个tomcat实例上。这样同一个客户的会话信息，只会存在于一台tomcat，就无需对session 进行处理。优点：简单。缺点：可能会出现负载失衡，可能使用同个ip出口，导致这些请求都转发到了一个tomcat 上。

如果就部署个两台，这种方式简单快捷，优先使用。

session 复制
将每个集群服务器的session信息，复制到其他服务器上，保持会话一致。缺点：实现难度大，消耗资源多。

session 共享
集群部署推荐方式，将session 数据都抽离到第三方容器中，每个服务器实例都可以操作这个第三方容器，实现对session 的存取。优点： 第三方容器数据容量相较于服务器内存大很多，服务实例中断，不会导致会话信息丢失。缺点：需要引入第三方组件，降低系统的稳定性，增加网络开销等。

Session 共享实现
数据容器：Redis 采用Redisson框架（具有内存中数据网格功能的Redis Java客户端）

集成session 框架：spring session

项目环境：spring boot 2.2.7

简单说下Redisson，通常使用该框架，来实现redis 分布式锁的实现。当然还包含了很多其他工程。可以与spring 的众多框架结合（spring boot ，spring data，spring cache ，spring session），官方地址：https://github.com/redisson/redisson

spring session 中文文档：https://www.springcloud.cc/spring-session.html

代码实践
代码参考：https://github.com/gengzi/gengzi_spring_security

核心依赖
spring session 与 redis 结合，只需要导入 spring-session-data-redis 。 无需再次引入 spring-session-core 。官方配置文档：https://docs.spring.io/spring-session/docs/2.4.1/reference/html5/guides/boot-redis.html

redisson 提供的 spring session 结合的配置和依赖：https://github.com/redisson/redisson/wiki/14.-Integration-with-frameworks#147-spring-session
————————————————
版权声明：本文为CSDN博主「耿子666」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_28817739/article/details/110898914
