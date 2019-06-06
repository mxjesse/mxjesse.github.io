### RabbitMQ Java Client应用

#### RabbitMQ介绍
>RabbitMQ是开源的消息中间件，服务器端用Erlang语言编写，基于AMQP标准实现（Advanced Message Queue Protocol 高级消息队列协议 - 应用层协议的一个开放标准），支持多种客户端，如：Python、Ruby、.NET、Java、C、PHP等。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面均有不错的表现。

#### 安装
建议在本地测试使用docker安装，具体安装方法可以百度一下。
  
`PS: RabbiqMQ的默认访问端口为5672，默认管理端口为15672`
### Springboot整合RabbitMQ
#### POM导入依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

#### application.properties配置
```
server.port=8080
# rabbitmq连接配置
spring.rabbitmq.addresses=localhost:5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
spring.rabbitmq.port=5672
```

#### 生产者代码

