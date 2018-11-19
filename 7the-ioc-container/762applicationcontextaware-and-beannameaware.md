当`ApplicationContext`创建实现`org.springframework.context.ApplicationContextAware`接口的对象实例时，将为该实例提供对该`ApplicationContext`的引用。

```
public interface ApplicationContextAware {

    void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
}
```



