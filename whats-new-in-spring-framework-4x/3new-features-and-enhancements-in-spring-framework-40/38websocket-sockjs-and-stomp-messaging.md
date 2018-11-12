一个新的`spring-websocket`模块为web应用程序中的客户端和服务器之间基于websocket的双向通信提供了全面的支持。它与[JSR-356](https://jcp.org/en/jsr/detail?id=356)兼容，Java WebSocket API，并且还提供了基于sockjs的回退选项\(即WebSocket模拟\)，用于还不支持WebSocket协议的浏览器\(如Internet Explorer &lt; 10\)。

一个新的`spring-messaging`传递模块添加了对STOMP的支持，将其作为WebSocket子协议，用于应用程序中，以及一个用于路由和处理来自WebSocket客户端的STOMP消息的注释编程模型。因此，`@Controller`现在可以同时包含`@RequestMapping`和`@MessageMapping`方法，用于处理来自websocket连接客户机的HTTP请求和消息。新的`spring-messaging`模块还包含以前Spring集成项目中的关键抽象，如Message、MessageChannel、MessageHandler和其他用于作为基于消息的应用程序基础的抽象。

有关更多细节，包括更全面的介绍，请参阅第26章WebSocket支持部分（[Chapter 26,WebSocket Support](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#websocket)）。

