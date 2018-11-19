当`ApplicationContext`创建实现`org.springframework.context.ApplicationContextAware`接口的对象实例时，将为该实例提供对该`ApplicationContext`的引用。

```
public interface ApplicationContextAware {

    void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
}
```

因此，bean可以通过ApplicationContext接口以编程方式操作创建它们的ApplicationContext，或者通过将引用强制转换为此接口的已知子类（例如ConfigurableApplicationContext）来公开其他功能。一种用途是对其他bean进行编程检索。有时这种能力很有用；但是，通常你应该避免它，因为它将代码耦合到Spring并且不遵循Inversion of Control样式，其中协作者作为属性提供给bean。ApplicationContext的其他方法提供对文件资源的访问，发布应用程序事件和访问MessageSource。这些附加功能在中描述[Section 7.15, “Additional capabilities of the ApplicationContext”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#context-introduction)。

从Spring 2.5开始，自动装配是另一种获取ApplicationContext引用的替代方法。

