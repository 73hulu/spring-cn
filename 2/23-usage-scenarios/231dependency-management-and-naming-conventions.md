依赖性管理和依赖性注入是两码事。要将Spring的这些优秀特性引入应用程序\(比如依赖注入\)，您需要组装所有所需的库\(jar文件\)，并在运行时\(甚至在编译时\)将它们放到classpath中。这些依赖项不是注入的虚拟组件，而是文件系统\(通常\)中的物理资源。依赖关系管理的过程包括定位这些资源、存储它们并将它们添加到类路径中。依赖关系可以是直接的\(例如，我的应用程序在运行时依赖于Spring\)，也可以是间接的\(例如，我的应用程序依赖于common -dbcp，后者依赖于common -pool\)。间接依赖也被称为“传递的”，而这些依赖是最难识别和管理的。

如果要使用Spring，您需要获得包含所需的Spring片段的jar库的副本。为了使这变得更容易，Spring被打包为一组模块，它们尽可能地分离依赖项，因此，例如，如果您不想编写web应用程序，那么您不需要Spring -web模块。为了在本指南中引用Spring库模块，我们使用了一个简单的命名约定Spring -\*或Spring -\*.jar，其中\*表示模块的短名称\(例如spring-core、spring-webmvc、spring-jms等\)。您使用的实际jar文件名通常是与版本号连接的模块名\(例如spring-core-4.3.20.RELEASE.jar\)。

Spring框架的每个版本都会将构件发布到以下地方:



* Maven中央仓库，这是Maven查询的默认存储库，不需要任何特殊配置。Spring所依赖的许多公共库也可以从Maven Central获得，Spring社区的很大一部分使用Maven进行依赖管理，因此这对它们来说很方便。这里jar的名称是spring-\*-&lt;version&gt;.jar 和Maven groupId是org.springframework
* 在专门为Spring托管的公共Maven存储库中。除了最终的GA版本，这个存储库还包含开发快照和里程碑。jar文件名与Maven Central的格式相同，因此这是一个很有用的地方，可以让Spring的开发版本与部署在Maven Central的其他库一起使用。这个存储库还包含一个包分发zip文件，该文件包含捆绑在一起的所有Spring jar，便于下载。

因此，您需要决定的第一件事是如何管理您的依赖关系:我们通常建议使用自动化系统，如Maven、Gradle或Ivy，但您也可以通过自己下载所有jar来手动完成。

下面您将找到Spring构件的列表。有关每个模块的更完整描述，请参阅第2.2节“框架模块”  [Section 2.2, “Framework Modules”](https://docs.spring.io/spring/docs/4.3.20.RELEASE/spring-framework-reference/htmlsingle/#overview-modules)。

表2.1。Spring框架构件

| GroupId | ArtifactId | Description |
| :---: | :--- | :--- |
| org.springframework | spring-aop | Proxy-based AOP support |
| org.springframework | spring-aspects | AspectJ based aspects |
| org.springframework | spring-beans | Beans support, including Groovy |
| org.springframework | spring-context | Application context runtime, including scheduling and remoting abstractions |
| org.springframework | spring-context-support | Support classes for integrating common third-party libraries into a Spring application context |
| org.springframework | spring-core | Core utilities, used by many other Spring modules |
| org.springframework | spring-expression | Spring Expression Language \(SpEL\) |
| org.springframework | spring-instrument | Instrumentation agent for JVM bootstrapping |
| org.springframework | spring-instrument-tomcat | Instrumentation agent for Tomcat |
| org.springframework | spring-jdbc | JDBC support package, including DataSource setup and JDBC access support |
| org.springframework | spring-jms | JMS support package, including helper classes to send/receive JMS messages |
| org.springframework | spring-messaging | Support for messaging architectures and protocols |
| org.springframework | spring-orm | Object/Relational Mapping, including JPA and Hibernate support |
| org.springframework | spring-oxm | Object/XML Mapping |
| org.springframework | spring-test | Support for unit testing and integration testing Spring components |
| org.springframework | spring-tx | Transaction infrastructure, including DAO support and JCA integration |
| org.springframework | spring-web | Foundational web support, including web client and web-based remotingorg.springframework |
| org.springframework | spring-webmvc | HTTP-based Model-View-Controller and REST endpoints for Servlet stacks |
| org.springframework | spring-webmvc-portlet | MVC implementation to be used in a Portlet environment |
| org.springframework | spring-websocket | WebSocket and SockJS infrastructure, including STOMP messaging support |

**Spring依赖和依赖于Spring**

尽管Spring为大量企业和其他外部工具提供了集成和支持，但它有意将强制依赖保持在绝对最小：为了在简单的用例中使用Spring，您不需要查找和下载\(甚至自动地\)大量的jar库。对于基本依赖项注入，只有一个强制的外部依赖项，即日志记录\(关于日志记录选项的更详细描述，请参阅下面\)。

接下来，我们将概述配置依赖于Spring的应用程序所需的基本步骤，首先使用Maven，然后使用Gradle，最后使用Ivy。在所有情况下，如果有什么不清楚的地方，请参考依赖关系管理系统的文档，或者查看一些示例代码——Spring在构建时使用Gradle来管理依赖关系，我们的示例主要使用Gradle或Maven。

**Maven依赖管理**

如果使用Maven进行依赖项管理，甚至不需要显式地提供日志依赖项。例如，要创建应用程序上下文并使用依赖注入配置应用程序，您的Maven依赖关系将如下所示：

```
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.3.20.RELEASE</version>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

就是这样。注意，如果您不需要针对Spring api进行编译，那么可以将范围声明为运行时，这通常是基本依赖注入用例的情况。

上面的示例与Maven中央存储库一起工作。要使用Spring Maven存储库\(例如用于里程碑或开发人员快照\)，您需要在Maven配置中指定存储库位置。

完整的版本：

```
<repositories>
    <repository>
        <id>io.spring.repo.maven.release</id>
        <url>http://repo.spring.io/release/</url>
        <snapshots><enabled>false</enabled></snapshots>
    </repository>
</repositories>
```

里程碑：

```
<repositories>
    <repository>
        <id>io.spring.repo.maven.milestone</id>
        <url>http://repo.spring.io/milestone/</url>
        <snapshots><enabled>false</enabled></snapshots>
    </repository>
</repositories>
```

快照：

```
<repositories>
    <repository>
        <id>io.spring.repo.maven.snapshot</id>
        <url>http://repo.spring.io/snapshot/</url>
        <snapshots><enabled>true</enabled></snapshots>
    </repository>
</repositories>
```

**Maven“材料清单”（BOM）依赖关系**

在使用Maven时，可能会意外地混合使用不同版本的Spring jars。例如，您可能会发现第三方库或其他Spring项目引入了对旧版本的传递依赖。如果您自己忘记显式地声明一个直接依赖项，那么可能会出现各种意想不到的问题。

为了克服这些问题，Maven支持“材料清单”\(BOM\)依赖的概念。您可以在dependencyManagement部分中导入spring-framework-bom，以确保所有spring依赖关系\(直接和传递\)都在同一个版本中。

```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>4.3.20.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

使用BOM的另一个好处是，当依赖于Spring框架构件时，您不再需要指定`<version>`属性:

```
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
    </dependency>
<dependencies>
```

**Gradle依赖管理**

要将Spring存储库与Gradle构建系统一起使用，请在存储库部分中包含适当的URL：

```
repositories {
    mavenCentral()
    // and optionally...
    maven { url "http://repo.spring.io/release" }
}
```

您可以根据需要将存储库URL从/release更改为/milestone或/snapshot。一旦配置了存储库，就可以按照通常的分级方式声明依赖关系：

```
dependencies {
    compile("org.springframework:spring-context:4.3.20.RELEASE")
    testCompile("org.springframework:spring-test:4.3.20.RELEASE")
}
```

#### Ivy**依赖管理**

如果您喜欢使用Ivy来管理依赖关系，那么还有类似的配置选项。

要将Ivy配置为指向Spring存储库，请将以下解析器添加到ivysettings.xml中：

```
<resolvers>
    <ibiblio name="io.spring.repo.maven.release"
            m2compatible="true"
            root="http://repo.spring.io/release/"/>
</resolvers>
```

您可以根据需要将根URL从/release/更改为/milestone/或/snapshot/。

一旦配置好，就可以按照通常的方式添加依赖项。例如\(在ivy.xml中\)：

```
<dependency org="org.springframework"
    name="spring-core" rev="4.3.20.RELEASE" conf="compile->runtime"/>
```

**Zip文件**

尽管使用支持依赖管理的构建系统是获得Spring框架的推荐方法，但仍然可以下载发行版zip文件。

zip文件发布到Spring Maven存储库\(这只是为了方便起见，您不需要Maven或任何其他构建系统来下载它们\)。

要下载发行版zip，请打开web浏览器[http://repo.spring.io/release/org/springframework/spring，并为您想要的版本选择适当的子文件夹。分发文件\`-dist.zip\`结尾，例如spring-framework-{spring-version}-](http://repo.spring.io/release/org/springframework/spring，并为您想要的版本选择适当的子文件夹。分发文件`-dist.zip`结尾，例如spring-framework-{spring-version}-) release -dist.zip。发行版还针对里程碑[milestones](https://repo.spring.io/milestone/org/springframework/spring)和快照[snapshots](https://repo.spring.io/snapshot/org/springframework/spring)发布。

