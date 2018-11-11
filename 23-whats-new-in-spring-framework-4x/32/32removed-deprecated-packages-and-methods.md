所有弃用的包和许多弃用的类和方法都已在4.0版中删除。如果您正在从以前的Spring版本升级，那么您应该确保您已经修复了您正在对过时api进行的任何废弃调用。

要获得完整的更改集，请查看API差异报告[API Differences Report](https://docs.spring.io/spring-framework/docs/3.2.4.RELEASE_to_4.0.0.RELEASE/)。

.请注意，可选的第三方依赖关系已经提高到2010/2011年的最小值\(例如，Spring 4通常只支持2010年末或以后发布的版本\):值得注意的是，Hibernate 3.6+、EhCache 2.1+、Quartz 1.8+、Groovy 1.8+和Joda-Time 2.0+。作为规则的一个例外，Spring 4需要最近的Hibernate验证器4.3+，现在对Jackson的支持主要集中在2.0+上\(Jackson 1.8/1.9的支持暂时保留在Spring 3.2的支持位置;现在只是用弃用的形式\)。

