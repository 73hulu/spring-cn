数据访问/集成层由JDBC、ORM、OXM、JMS和事务模块组成。

`spring-jdbc`模块提供了一个JDBC抽象层，它消除了对特定于数据库供应商的错误代码进行冗长的JDBC编码和解析的需要。

`spring-tx` 模块为实现特殊接口的类和所有pojo\(普通的Java对象\)支持编程和声明式事务管理。

`spring-orm` 模块为流行的对象关系映射api\(包括JPA、JDO和Hibernate\)提供了集成层。使用`Spring -orm`模块，您可以将所有这些O/R映射框架与Spring提供的所有其他特性结合使用，例如前面提到的简单声明式事务管理特性。

`spring-oxm`模块提供了一个抽象层，支持对象/XML映射实现，比如JAXB、Castor、XMLBeans、JiBX和XStream。

`spring-jms` 模块\(Java消息传递服务\)包含用于生成和消费消息的特性。由于Spring Framework 4.1，它提供了与Spring消息传递模块的集成。

