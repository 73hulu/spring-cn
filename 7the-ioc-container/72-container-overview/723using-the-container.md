`ApplicationContext`是高级工厂的接口，能够维护不同bean及其依赖项的注册表。使用方法`T getBean（String name，Class <T> requiredType）`，您可以检索bean的实例。

ApplicationContext使您可以读取bean定义并按如下方式访问它们：

```
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

使用Groovy配置，bootstrapping看起来非常相似，只是一个不同的上下文实现类，它是Groovy感知的（但也理解XML bean定义）：

```
ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```

最灵活的变体是`GenericApplicationContext`与reader delegates的组合，例如， 使用XML文件的`XmlBeanDefinitionReader`：

```
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
context.refresh();
```

或者使用`GroovyBeanDefinitionReader `for Groovy文件：

```
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
context.refresh();
```

这些reader delegates可以在同一个ApplicationContext上混合和匹配，如果需要，可以从不同的配置源读取bean定义。

您可以使用getBean来检索Bean的实例。`ApplicationContext`接口有一些其他方法可以检索bean，但理想情况下，您的应用程序代码绝不应该使用它们。实际上，您的应用程序代码根本不应该调用getBean（）方法，因此根本不依赖于Spring API。例如，Spring与Web框架的集成为各种Web框架组件（如控制器和JSF托管bean）提供依赖注入，允许您通过元数据（例如自动装配注释）声明对特定bean的依赖性。



