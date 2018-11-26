> 注释是否比配置Spring的XML更好？
>
> 基于注释配置的引入引发了这种方法是否比XML更“好”的问题。简而言之，这要看情况而定。完整的回答是每种方式都有自己的有点和缺点，这要看开发者决定用哪种方式更适合自己。由于它的定义方式，注释在它们的声明中提供了很多上下文，从而导致更短和更简洁的配置。但是，XML擅长在不触及源代码或重新编译它们的情况下连接组件。一些开发者倾向于封闭的源代码，而另一些开发者排斥使用注解类，因为这样不再POJO，而且这样的配置方式变的不再集中管理并且很难控制。
>
> 无论选择如何，Spring都可以兼顾两种风格，甚至可以将它们混合在一起。值得一提的是，通过[JavaConfig](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-java)方式，Spring允许以一种非侵入的方式使用注解，这样组件代码和工具就不会被侵入，通过[Spring Tool Suite](https://spring.io/tools/sts)支持全部的配置风格。



