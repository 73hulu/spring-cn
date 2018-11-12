对核心容器进行了一些一般性的改进：

* Spring现在将泛型类型视为注入bean时的限定符形式（[_generic types\_as a form of\_qualifier_](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-generics-as-qualifiers)） 。例如，如果您正在使用Spring数据存储库，您现在可以轻松地注入特定的实现：`@Autowired Repository<Customer> customerRepository`
* 如果您使用Spring的元注释支持，您现在可以开发自定义注释来公开源注释的特定属性（[expose specific attributes from the source annotation](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-meta-annotations)）

* .现在，beans被自动连接到列表和数组中时（[autowired into lists and arrays](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-autowired-annotation)）它们可以被排序。支@Order\`注释和Ordered接口。

* `@Lazy`注释现在可以用于注入点，以及@Bean定义。

* 为使用基于java的配置的开发人员引入了`@Description`注释。

* 通过`@Conditional`注释添加了一个用于条件过滤bean（[conditionally filtering beans](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java-conditional)）的通用模型。这类似于`@Profile`，但允许以编程方式开发用户定义的策略。

* 基于cglib的代理类（[CGLIB-based proxy classes](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#aop-pfb-proxy-types)） 不再需要默认构造函数。支持是通过[objenesis](https://code.google.com/p/objenesis/) 库提供的，该库作为Spring框架的一部分被重新内联和分发。有了这种策略，就再也不会为代理实例调用构造函数了。



