# RabbitMQ Java Client应用

#### RabbitMQ介绍
>RabbitMQ是开源的消息中间件，服务器端用Erlang语言编写，基于AMQP标准实现（Advanced Message Queue Protocol 高级消息队列协议 - 应用层协议的一个开放标准），支持多种客户端，如：Python、Ruby、.NET、Java、C、PHP等。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面均有不错的表现。

#### RabbitMQ几个概念
* Broker：（中间人）消息队列的服务实体
* Exchange：消息交换机，指定消息按照相应的规则，路由到对应的Queue
* Queue：消息队列，每个消息都会被放入对应的消息队列
* Bingding：绑定，将Exchange和对应的Queue按照路由规则绑定
* Routing Key：路由键，消息头的一个属性，用来标记消息的路由规则，决定了Exchange的转发路径，最大长度为255字节。
* Virtual Host：RabbitMq上可以创建多个虚拟的Message Broker，叫做Virtual Hosts（vHosts）。每一个vHost本质上是一个mini-rabbitmq server，分别管理自己的exchange和binding。vhost相当于物理的server，可以为不同app提供边界隔离，使得应用安全的运行在不同的vhost实例上，相互之间不会干扰。producer和consumer连接rabbit server需要指定一个vhost。
* Producer：生产者，消息生产方
* Consumer：消费者，消息接收方

##### Exchange消息转发策略
消息交换机的转发策略，是指Exchange收到生产者发送的消息后根据什么规则把消息发送到一个或者多个队列中保存。
转发策略主要和三个因素相关：

	1.Exchange Type（Exchange的类型）
	2.Binging Key（Exchange和Queue的绑定关系）
	3.消息的标记信息（Routing Key和headers）
	
1.Fanout（广播模式）

	交换机会把发送到该交换机的消息路由到所有与该交换机绑定的队列中。 	
2.Direct（路由模式）`RabbitMQ默认使用此模式`

	精确匹配，当消息的RoutingKey和BindingKey完全匹配，则将消息发送到此Queue。
	RabbitMQ默认提供了一个	Exchange，名字为空字符串，类型为Direct，绑定到所有的Queue（每个Queue与这个无名Exchange之间的BindingKey是Queue的名称），所以我们没使用交换机的时候使用的是默认的Exchange。
3.Topic（通配符模式）

	按照正则表达式模糊匹配，用消息的RoutingKey和BindingKey进行模糊匹配，匹配成功则将消息发送到对应Queue。
	RoutingKey是以"."分隔的字符串，例如"first.last"，BindingKey和RoutingKey一样也为"."分隔的字符串，BindingKey中有两种通配符
	* "*"：用于匹配一个单词
	* "#"：用于匹配多个单词，可以为0个
4.Headers（键值对模式）

	此模式不依赖RoutingKey和BindingKey的匹配规则来转发消息，交换机的路由规则是通过消息头的Headers属性来进行匹配转发，类似于Http请求的header。
	
	在绑定Queue和Exchange时指定一组键值对，键值对包含一个一个键"x-match"，这个键的值可以是"any"和"all"，"all"代表消息携带的Hash要完全匹配，"any"代表匹配一个以上的键。
	
	当消息发送到Exchange，交换机会获取到该消息的Headers，对比其中的键值对是否完全匹配Queue和Exchange绑定时指定的BindingKey，如果匹配则转发到此Queue。
	
5.RPC（RPC模式）
	
此模式不多介绍了，具体参见这里:[RabbitMQ RPC](https://www.cnblogs.com/LiangSW/p/6216537.html)
	
##### 消息确认机制 Message Acknowledge


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
