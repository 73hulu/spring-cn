由于按类型自动装配可能会导致多个候选人，因此通常需要对选择过程有更多控制权。实现这一目标的一种方法是使用Spring的@Primary注释。@Primary表示当多个bean可以自动装配到单值依赖项时，应该优先选择特定的bean。

假设我们有以下配置将firstMovieCatalog定义为主要MovieCatalog。

```
@Configuration
public class MovieConfiguration {

    @Bean
    @Primary
    public MovieCatalog firstMovieCatalog() { ... }

    @Bean
    public MovieCatalog secondMovieCatalog() { ... }

    // ...
}
```



