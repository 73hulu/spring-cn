从Spring Framework 4.0开始，可以使用Groovy DSL定义外部bean配置。这在概念上类似于使用XML bean定义，但允许更简洁的语法。使用Groovy还允许您轻松地直接在引导代码中嵌入bean定义。例如:

```
def reader = new GroovyBeanDefinitionReader(myApplicationContext)
reader.beans {
    dataSource(BasicDataSource) {
        driverClassName = "org.hsqldb.jdbcDriver"
        url = "jdbc:hsqldb:mem:grailsDB"
        username = "sa"
        password = ""
        settings = [mynew:"setting"]
    }
    sessionFactory(SessionFactory) {
        dataSource = dataSource
    }
    myService(MyService) {
        nestedBean = { AnotherBean bean ->
            dataSource = dataSource
        }
    }
}
```

有关更多信息，请参阅GroovyBeanDefinitionReader [javadocs](https://docs.spring.io/spring-framework/docs/4.3.20.RELEASE/javadoc-api/org/springframework/beans/factory/groovy/GroovyBeanDefinitionReader.html)。



