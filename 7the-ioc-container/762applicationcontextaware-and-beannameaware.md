当`ApplicationContext`创建实现`org.springframework.context.ApplicationContextAware`接口的对象实例时，将为该实例提供对该`ApplicationContext`的引用。

```
public interface ApplicationContextAware {

    void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
}
```

因此，bean可以通过ApplicationContext接口以编程方式操作创建它们的ApplicationContext，或者通过将引用强制转换为此接口的已知子类（例如ConfigurableApplicationContext）来公开其他功能。一种用途是对其他bean进行编程检索。有时这种能力很有用；但是，通常你应该避免它，因为它将代码耦合到Spring并且不遵循Inversion of Control样式，其中协作者作为属性提供给bean。ApplicationContext的其他方法提供对文件资源的访问，发布应用程序事件和访问MessageSource。这些附加功能在中描述[Section 7.15, “Additional capabilities of the ApplicationContext”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#context-introduction)。

从Spring 2.5开始，自动装配是另一种获取ApplicationContext引用的替代方法。“传统”构造函数和byType自动装配模式（如第7.4.5节“自动装配协作者”中所述[Section 7.4.5, “Autowiring collaborators”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-autowire)）可以分别为构造函数参数或setter方法参数提供ApplicationContext类型的依赖关系。要获得更大的灵活性，包括自动装配字段和多参数方法的功能，请使用基于注释的新自动装配功能。如果这样做，ApplicationContext将自动装入一个字段，构造函数参数或方法参数，如果相关的字段，构造函数或方法带有@Autowired注释，那么该参数将期望得到ApplicationContext类型。有关更多信息，详见[Section 7.9.2, “@Autowired”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#beans-autowired-annotation)。

