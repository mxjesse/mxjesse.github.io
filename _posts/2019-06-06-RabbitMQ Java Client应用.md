# RabbitMQ Java Client应用

#### RabbitMQ介绍
>RabbitMQ是开源的消息中间件，服务器端用Erlang语言编写，基于AMQP标准实现（Advanced Message Queue Protocol 高级消息队列协议 - 应用层协议的一个开放标准），支持多种客户端，如：Python、Ruby、.NET、Java、C、PHP等。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面均有不错的表现。

#### 安装
建议在本地测试使用docker安装，具体安装方法可以百度一下。
  
`PS: RabbiqMQ的默认访问端口为5672，默认管理端口为15672`
## Springboot整合RabbitMQ
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
```
/**
 * @author mx
 * @date 2019/6/6 4:10 PM
 */
@RestController
@RequestMapping("producer")
public class ProducerController {

    @Autowired
    private AmqpTemplate amqpTemplate;
[]()
    @RequestMapping("/send")
    public String send(@RequestParam("msg") String msg) {

        String context = msg + "======" + new Date();
        // 往队列里发送消息，队列名为 "myQueue",自行在rabbitMQ管理端新建
        amqpTemplate.convertAndSend("myQueue", context);

        return "Success!";
    }

}
```
手动在RabbitMQ管理服务器新建名为“myQueue”的队列
![新建队列](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_floder/201906/1559809389451.jpg)

#### 消费者代码
```
/**
 * @author mx
 * @date 2019/6/6 4:15 PM
 */
@Component
@RabbitListener(queues = {"myQueue"}) // 监听名为myQueue的队列
public class ConsumerListener {

    /**
     * 处理接收到的信息
     */
    @RabbitHandler
    public void handle(String msg) {

        System.out.println(msg);
    }
}
```
#### 测试消息发送和消费方法
在浏览器打开RabbitMQ的管理页面[http://127.0.0.1:8080/producer/send?msg=你好，朋友](http://127.0.0.1:8080/producer/send?msg=%E4%BD%A0%E5%A5%BD%EF%BC%8C%E6%9C%8B%E5%8F%8B)

![浏览器访问发送消息地址](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_floder/201906/1559809684382.jpg)

在控制台看到输出如下：
![浏览器访问发送消息地址](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_floder/201906/1559809707954.jpg)
