对核心容器进行了一些一般性的改进：

* Spring现在将泛型类型视为注入bean时的限定符形式（[_generic types_as a form of_qualifier_](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-generics-as-qualifiers)） 。例如，如果您正在使用Spring数据存储库，您现在可以轻松地注入特定的实现：`@Autowired Repository<Customer> customerRepository`
* 如果您使用Spring的元注释支持，您现在可以开发自定义注释来公开源注释的特定属性（[expose specific attributes from the source annotation](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-meta-annotations)）

* .现在，beans被自动连接到列表和数组中时（[autowired into lists and arrays](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-autowired-annotation)）它们可以被排序。支@Order\`注释和Ordered接口。

*  `@Lazy`注释现在可以用于注入点，以及@Bean定义。

*  为使用基于java的配置的开发人员引入了`@Description`注释。



