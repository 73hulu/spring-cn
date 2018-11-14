实例化Spring IoC容器非常简单。 提供给`ApplicationContext`构造函数的位置路径实际上是资源字符串，允许容器从各种外部资源（如本地文件系统，Java `CLASSPATH`等）加载配置元数据。

```
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```



