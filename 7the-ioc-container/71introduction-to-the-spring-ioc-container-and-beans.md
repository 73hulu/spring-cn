本章涵盖了Spring框架实现IOC[\[1\]](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#ftn.d5e1829)的原理。IoC也称为依赖注入\(DI\)。它是一个对象定义依赖关系的过程，即对象只能通过构造函数参数、工厂方法的参数或对象实例在构造或从工厂方法返回后设置的属性来定义与之工作的其他对象。然后容器在创建bean时注入这些依赖项。这个过程从根本上说是bean本身创建的逆向\(因此称为控制反转\(IoC\)\)，它通过使用类的直接构造或服务定位器模式等机制来控制依赖项的实例化或位置。

`org.springframework.beans`和`org.springframework.context`包是Spring框架的IoC容器的基础。`BeanFactory`接口提供了一种高级配置机制，能够管理任何类型的对象。`ApplicationContext`是`BeanFactory`的子接口。它更容易跟Spring AOP特性集成；消息资源处理\(用于国际化\)，事件发布；以及应用程序层特定的上下文，例如用于web应用程序的`WebApplicationContext`。

简而言之，`BeanFactory`提供了配置框架和基本功能，而`ApplicationContext`添加了更多企业特定的功能。ApplicationContext是BeanFactory的完整超集，本章专门用于描述的Spring的IoC容器。有关使用BeanFactory而不是ApplicationContext的更多信息，请参阅[7.16, “The BeanFactory”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-beanfactory)。

在Spring中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。bean是由Spring IoC容器实例化、组装和以其他方式管理的对象。否则，bean只是应用程序中的众多对象之一。bean及其之间的依赖关系反映在容器使用的配置元数据中。

