通过用`@JmsListener`注释bean方法，Spring 4.1引入了一种更简单的基础结构来注册JMS侦听器端点（[to register JMS listener endpoints](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#jms-annotated)）。XML命名空间已经得到了增强，以支持这种新样式\(`jms:annotation-driven`\)，而且还可以使用Java config \(`@EnableJms`、`JmsListenerContainerFactory`\)完全配置基础结构。还可以使用`JmsListenerConfigurer`以编程方式注册侦听器端点。

Spring 4.1还支持JMS，允许您从4.0引入的spring-messaging抽象中获益，即:

* 消息侦听器端点可以具有更灵活的签名，并受益于诸如`@Payload`、`@Header`、`@Headers`和`@SendTo`等标准消息传递注释。也可以使用标准消息代替javax.jms.Message作为方法参数。
* 一个新的`JmsMessageOperations`接口可用，允许使用`Message`消息抽象进行类似于`JmsTemplate`的操作。



