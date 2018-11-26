`@Required`注释适用于bean属性setter方法，如下例所示：

```
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Required
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```

此注释仅指示必须在配置时通过bean定义中的显式属性值或通过自动装配填充受影响的bean属性。如果没有传入这个值，容器会抛出异常，这样你就能够明确知道哪儿失败了，从而避免在之后再抛出一个空指针错误。即使是这样，我们仍然推荐你在相关的 bean 类中做断言，例如在初始化方法中。即使您在容器外使用类，这样做也会强制执行那些必需的引用和值。

