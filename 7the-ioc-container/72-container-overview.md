org.springframework.context.ApplicationContext接口表示Spring IoC容器，并负责实例化、配置和组装上述bean。容器通过读取配置元数据获得关于实例化、配置和组装对象的指令。**配置元数据用XML、Java注释或Java代码表示**。它允许您表达组成应用程序的对象以及这些对象之间的丰富相互依赖关系。

随Spring一起提供了ApplicationContext接口的几种实现。在独立应用程序中，创建ClassPathXmlApplicationContext或FileSystemXmlApplicationContext实例是很常见的。虽然XML一直是定义配置元数据的传统格式，但您可以通过提供少量XML配置来声明支持这些额外的元数据格式，指示容器使用Java注释或代码作为元数据格式。

在大多数应用程序场景中，用户代码不需要显式来实例化Spring IoC容器的一个或多个实例。例如，在web应用程序场景中，应用程序的web.xml文件中的简单八行（左右）样板Web描述符XML通常就足够了\(参见[Section 7.15.4, “Convenient ApplicationContext instantiation for web applications”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#context-create)\)。如果您使用基于eclipse的 Spring Tool Suite工具套件开发环境，那么只需单击鼠标或敲击键盘就可以轻松地创建这个样板配置。

下图是Spring工作原理的高级视图。您的应用程序类与配置元数据相结合，以便在创建和初始化ApplicationContext之后，您拥有一个完全配置且可执行的系统或应用程序。

