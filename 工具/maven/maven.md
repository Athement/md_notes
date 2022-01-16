## Maven 是什么？

Maven 主要服务于基于 Java 平台的项目构建、依赖管理和项目信息管理。

Maven的主要功能主要分为5点

- 依赖管理系统
- 多模块构建
- 一致的项目结构
- 一致的构建模型
- 插件机制

**为什么选用 Maven 进行构建？**

- Maven 是优秀的项目构建工具。可以方便的对项目进行分模块构建，这样在开发和测试打包部署时，效率会提高很多。
- Maven 可以进行依赖的管理。可以将不同系统的依赖进行统一管理，并且可以进行依赖之间的传递和继承。

**Maven 规约是什么？**

- `/src/main/java/` ：Java 源码。
- `/src/main/resource` ：Java 配置文件，资源文件。
- `/src/test/java/` ：Java 测试代码。
- `/src/test/resource` ：Java 测试配置文件，资源文件。
- `/target` ：文件编译过程中生成的 `.class` 文件、jar、war 等等。
- `pom.xml` ：配置文件

Maven 要负责项目的自动化构建，以编译为例，Maven 要想自动进行编译，那么它必须知道 Java 的源文件保存在哪里，这样约定之后，不用我们手动指定位置，Maven 能知道位置，从而帮我们完成自动编译。

Maven遵循**“约定>>>配置>>>编码”**。这样既减少了工作量，也能防止出错。

## Maven 常用命令

- `mvn archetype：create` ：创建 Maven 项目。
- `mvn compile` ：编译源代码。
- `mvn deploy` ：发布项目。
- `mvn test-compile` ：编译测试源代码。
- `mvn test` ：运行应用程序中的单元测试。
- `mvn dependency`:对依赖进行操作
  - `mvn dependency:sources`用于下载jar包对应的source（mvn dependency:sources -DincludeArtifactIds=guava）
- `mvn site` ：生成项目相关信息的网站。
- `mvn clean` ：<font color='cornflowerblue'>清除项目目录中的生成结果。</font>
- `mvn package` ：<font color='cornflowerblue'>根据项目生成的 jar/war 等。</font>
- `mvn install` ：在本地 Repository 中安装 jar 。
- `mvn eclipse:eclipse` ：生成 Eclipse 项目文件。
- `mvn jetty:run` 启动 Jetty 服务。
- `mvn tomcat:run` ：启动 Tomcat 服务。
- `mvn clean package -Dmaven.test.skip=true` ：清除以前的包后重新打包，跳过测试类。

## Maven 有哪些优点和缺点

 **1）优点**

- 简化了项目依赖管理。

  > 解决了依赖不完整和依赖冲突的问题

- 采用约定优于配置的策略,易于上手

- 便于与持续集成工具(Jenkins)<font color='cornflowerblue'>整合</font>。

- 便于项目<font color='cornflowerblue'>升级</font>，无论是项目本身升级还是项目使用的依赖升级。

- 有助于<font color='cornflowerblue'>多模块</font>项目的开发，一个模块开发好后，发布到仓库，依赖该模块时可以直接从仓库更新，而不用自己去编译。

- Maven 有很多插件，便于功能<font color='cornflowerblue'>扩展</font>，比如生产站点，自动发布版本等。

 **2）缺点**

- Maven 是一个庞大的构建系统，全面的学习难度大。

- Maven 采用约定优于配置的策略(convention over configuration)，虽然上手容易，但是一旦出了问题，难于调试。

- 当依赖很多时，m2eclipse 老是搞得 Eclipse 很卡。

  > 使用 IDEA ，而不是 Eclipse ，完美解决。

- 中国的网络环境差，很多 repository 无法访问，比如 Google Code、 JBoss 仓库无法访问等。

  > 这个也好解决，在 `<mirrors>` 中增加阿里巴巴的 Maven 私服，具体可以参见 [《提高 Maven 速度 —— Maven 仓库修改成国内阿里巴巴地址》](https://my.oschina.net/af8991/blog/833513) 文章。

## 对比其它构建工具

 **什么是构建？**

写完代码之后需要进行编译和运行

> 使用 IDE 写完代码，需要进行编译，再生成 war 包，以便部署到 Tomcat。

无构建工具

> 编写 Java 代码可能调用许多第三方的 API ，需要将jar 包下载到本地，然后添加进入工程，在 IDE 中进行添加设置。
>
> 这种方式非常繁琐，并且在遇到版本升级，Git 同步等时候，程序会变得非常脆弱，极易产生未知错误

有构建工具

> 构建工具可以让我们专注于写代码，而不需要考虑如何导入 jar 包，如何升级 jar 包版本，以及 git 多人协作等等问题。
>
> 在编译过程中的优势，在运行和发布的过程中，构建工具依然可以帮助我们将工程生成指定格式的文件。

Java三大构建工具：Ant、Maven和Gradle

[**Maven 和 Gradle 对比？**](https://www.cnblogs.com/huang0925/p/5209563.html)

**Maven 和 Ant 有什么区别？**

## Maven 坐标的含义？

```xml
<!-- FROM https://github.com/junit-team/junit4/blob/master/pom.xml -->
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<version>4.13-BETA</version>
```

Maven 使用坐标进行**唯一标识**。Maven 的坐标元素包括 groupId、artifactId、version、packaging、classfier 。

- **groupId** ：定义当前 Maven 项目隶属的实际项目。
- **artifactId** ：该元素定义当前实际项目中的一个 Maven 项目(模块)。推荐的做法是使用实际项目名称作为 artifactId 的前缀。
- **version** ：该元素定义了使用构件的版本。
- **packaging** ：定义 Maven 项目打包的方式，使用构件的什么包。打包方式通常与所生成构件的文件扩展名对应。默认为 jar 包。
- **classifier** ：该元素用来帮助定义构建输出的一些附件。附属构件与主构件对应。

*`groupId`、`artifactId`、`version` 是必须定义的。*

 **Maven 版本规则？**

Maven 主要是这样定义版本规则的：`<主版本>.<次版本>.<增量版本>`

- 主版本，一般来说代表了项目的重大的架构变更
- 次版本，一般代表了一些功能的增加或变化
- 增量版本，一般是一些小的 bug fix

 **[多模块如何聚合？](https://blog.csdn.net/zdq0394123/article/details/9634061)**

聚合配置一个打包类型为 `pom` 的聚合模块，然后在该 `pom` 中使用<modules/\>元素声明要聚合的模块

```xml
<modules>
	<module>orchid-server</module>
	<module>orchid-support</module>
</modules>
```

与聚合相对应的是继承，继承需要在子项目的配置中加入<parent\>配置

```xml
<modelVersion>4.0.0</modelVersion>
<parent>
		<groupId>com.stear.orchid</groupId>
		<artifactId>orchid</artifactId>
		<version>1.0-SNAPSHOT</version>
</parent>
<artifactId>orchid-support</artifactId>
```

## Maven `<dependencie />` 是什么？

`<dependencie />` 引入依赖关系。属性如下：

- `groupId` ：依赖项的 `groupId` 。

- `artifactId` ：依赖项的 `artifactId` 。

- `version` ：依赖项的 `version` 。

- `scope` ：依赖项的适用范围。

  > - `compile` ：默认值，适用于所有阶段（开发、测试、部署、运行），本 jar 会一直存在所有阶段。
  > - `provided` ：只在开发、测试阶段使用，目的是不让 Servlet 容器和你本地仓库的 jar 包冲突 。如 `servlet.jar` 。
  > - `runtime` ：只在运行时使用，如 JDBC 驱动，适用运行和测试阶段。
  > - `test` ：只在测试时使用，用于编译和运行测试代码，不会随项目发布。
  > - `system` ：类似 `provided` ，需要显式提供包含依赖的 jar 包，Maven 不会在 Repository 中查找它。
  > - `import` ：用于一个 `<dependencyManagement />` 对另一个 `<dependencyManagement />` 的继承。非常重要，通过它，可以实现类似 [《Maven Spring BOM (bill of materials)》](https://www.cnblogs.com/YLsY/p/5711103.html) 的功能。

- `exclusions` ：排除项目中的依赖冲突时使用。

**[`<dependencie />`和`<dependencyManagement />` 区别是什么](https://blog.csdn.net/qq_42288638/article/details/109051622)？**

- \<dependencyManagement /> ， 统一了 Maven 中依赖的版本号，定义在 \<dependencie /> 中的依赖，在不指定具体版本号时，就会沿着上层找到 <dependencyManagement /> 中的依赖，并使用它的版本号。

  > 当有多个子项目引用同一个依赖时，就不需要重复声明各自的版本号，只需统一使用 \<dependencyManagement /> 中的版本号即可。

- 所有声明在<dependencies /\>里的依赖都会自动引入，并默认被所有的子项目继承。

**对于一个多模块项目，如何管理项目依赖的版本？**

- 继承的方式:通过在父模块中声明 `<dependencyManagement />` 和`<pluginManagement />`， 然后让子模块通过元素指定父模块，这样子模块在定义依赖是就可以只定义 `groupId` 和 `artifactId`，自动使用父模块的 `version` ，这样统一整个项目的依赖的版本。
- [组合的方式](https://blog.csdn.net/LoveJavaYDJ/article/details/86594226):使用 `<dependencie />` 声明 `<scope />` 为 `import` 的依赖，从而引入一个 pom 的`<dependencyManagement />` 的。

## Maven 依赖的解析机制是怎么样的？

1. 解析发布(RELEASE)版本：如果本地有，直接使用本地的，没有才向远程仓库请求。
2. 解析快照(SNAPSHOT)版本：合并本地和远程仓库的元数据文件 `groupId/artifactId/version/maven-metadata.xml` ，这个文件存的版本都是带时间戳的，将最新的一个改名为不带时间戳的格式供本次编译使用。
3. 解析版本为 LATEST 过于复杂，且解析的结果不稳定，不推荐在项目中使用

**[RELEASE与SNAPSHOT区别](http://www.huangbowen.net/blog/2016/01/29/understand-official-version-and-snapshot-version-in-maven/)**

项目依赖RELEASE版本,只有在本地仓库没有时才向远程仓库请求

项目依赖SNAPSHOT版本,根据updatePolicy属性设置的频率从远程仓库请求

**Maven 依赖原则**

- 1.依赖路径最短优先原则。

  > 一个项目 Demo 依赖了两个 jar 包，其中 `A-B-C-X(1.0)` ， `A-D-X(2.0)` 。由于 `X(2.0)` 路径最短，所以项目使用的是 `X(2.0)` 。

- 2.pom文件中申明顺序优先。

  > 如果 `A-B-X(1.0)` ，`A-C-X(2.0)` 这样的路径长度一样怎么办呢？这样的情况下，Maven 会根据 pom 文件声明的顺序加载，如果先声明了 B ，后声明了 C ，那就最后的依赖就会是 `X(1.0)` 。

- 3.覆写优先

  > 子 pom 内声明的优先于父 pom 中的依赖。

**如何解决 jar 冲突？**

分析jar冲突

> 通过们 `mvn dependency:tree` 查看依赖树，或者使用 [IDEA Maven Helper](https://plugins.jetbrains.com/plugin/7179-maven-helper) 插件。

解决jar冲突

- 通过 Maven 的依赖原则来调整坐标在 pom 文件的申明顺序是最好的办法

- 使用将冲突中不想要的 jar 引入的 jar 进行 `<exclusions>` 掉。

## Maven 生命周期是怎么样的？

Maven 中有三个独立的生命周期：

- 1、Clean
- 2、Default
- 3、Site

每个生命周期的特点

- 每个生命周期包含一些阶段，阶段是有顺序的，后面的阶段依赖于前面的阶段。

- 不管用户要求执行的命令对应生命周期中的哪一个阶段，Maven都会自动从当前生命周期的最初位置开始执行，直到完成用户下达的指令

一个完整的项目构建过程通常包括清理、编译、测试、打包、集成测试、验证、部署等步骤，Maven 从中抽取了一套完善的、易扩展的生命周期。Maven 的生命周期是抽象的，其中的具体任务都交由插件来完成。Maven 为大多数构建任务编写并绑定了默认的插件，如针对编译的插件：`maven-compiler-plugin` 。用户也可自行配置或编写插件。

Maven有三套相互独立的生命周期，分别是 Clean、Default 和 Site。

- 1、Clean 生命周期：清理项目，包含三个 phase ：
  - pre-clean：执行清理前需要完成的工作。
  - clean：清理上一次构建生成的文件。
  - post-clean：执行清理后需要完成的工作
- 2、Default 生命周期：构建项目，重要的 phase 如下：
  - validate：验证工程是否正确，所有需要的资源是否可用。
  - compile：编译项目的源代码。
  - test：使用合适的单元测试框架来测试已编译的源代码。这些测试不需要已打包和布署。
  - package：把已编译的代码打包成可发布的格式，比如 jar、war 等。
  - integration-test：如有需要，将包处理和发布到一个能够进行集成测试的环境。
  - verify：运行所有检查，验证包是否有效且达到质量标准。
  - install：把包安装到maven本地仓库，可以被其他工程作为依赖来使用。
  - deploy：在集成或者发布环境下执行，将最终版本的包拷贝到远程的repository，使得其他的开发者或者工程可以共享。
- 3、Site 生命周期：建立和发布项目站点，phase 如下：
  - pre-site：生成项目站点之前需要完成的工作
  - site：生成项目站点文档
  - post-site：生成项目站点之后需要完成的工作
  - site-deploy：将项目站点发布到服务器

**我们经常使用 mvn clean package 命令进行项目打包，请问该命令执行了哪些动作来完成该任务？**

在这个命令中我们调用了 Maven 的 clean 周期的 clean 阶段绑定的插件任务，以及 default 周期的 package 阶段绑定的插件任务。

## 什么是 Maven 插件？

Maven 生命周期的每一个阶段的具体实现都是由 Maven 插件实现的。插件通常提供了一个目标的集合，并且可以使用下面的语法执行：`mvn [plugin-name]:[goal-name]`

Maven 提供了下面两种类型的插件：

- Build plugins ：在构建时执行，并在 `pom.xml` 的 元素中配置。
- Reporting plugins ：在网站生成过程中执行，并在 `pom.xml` 的元素中配置。

常用插件的列表：

- clean ：构建之后清理目标文件。删除目标目录。
- compiler ：编译 Java 源文件。
- surefile ：运行 JUnit 单元测试。创建测试报告。
- jar ：从当前工程中构建 JAR 文件。
- war ：从当前工程中构建 WAR 文件。
- javadoc ：为工程生成 Javadoc 。
- antrun ：从构建过程的任意一个阶段中运行一个 ant 任务的集合。

? **如何实现自定义插件？**

大多数情况下，我们不太需要开发自定义的 Maven 插件，并且面试一般也不会问。当然，感兴趣的胖友，可以看看 [《Maven 自定义插件开发》](https://blog.csdn.net/u012620150/article/details/78652624) 。

 **Maven 插件的解析机制？**

> 艿艿：这个问题，选择性理解即可。

当我们输入 `mvn dependency:tree` 这样的指令，解析的步骤为：

- 1、解析 `groupId` :

  > Maven 使用默认的 groupId 插件为 `org.apache.maven.plugins` 或者 `org.codehaus.mojo` 。

- 2、解析 `artifactId` (Maven 的官方叫做插件前缀解析策略)：

  > 合并该 `groupId` 在所有仓库中的元数据库文件（`maven-metadata-repository.xml`），比如 Maven 官方插件的元数据文件所在的目录为 `org\apache\maven\plugins` ，该文件下有如下的条目：
  >
  > ```
  > <plugin>
  >   <name>Maven Dependency Plugin</name>
  >   <prefix>dependency</prefix>
  >   <artifactId>maven-dependency-plugin</artifactId>
  > </plugin>
  > ```

  - 通过比较这样的条目，我们就将该命令的 `artifactId` 解析为`maven-dependency-plugin` 。

- 3、解析 `version` ：

  > 如果你在项目的 `pom` 中声明了该插件的版本，那么直接使用该版本的插件，否则合并所有仓库中 `groupId/artifactId/maven-metadata-repository.xml` ，找到最新的发布版本。

对于非官方的插件，有如下两个方法可以选择：

- 使用 `groupId:artifactId:version:goal` 来运行。
- 在 `settings.xml` 中添加 `pluginGroup` 项，这样 Maven 不能在官方的插件库中解析到某个插件，那么就可以去你配置的 `group` 下查找啦。

## 什么是 Maven 仓库？

**Maven 的仓库只有两大类：**

- 1、本地仓库。
- 2、远程仓库。在远程仓库中又分成了 3 种：
  - 中央仓库。
  - 私服。
  - 其它公共库。

**Maven 会先搜索本地仓库（repository），发现本地没有然后从远程仓库（中央仓库）获取。**

- 但中央仓库只有一个，最好从其镜像处下载。国内可以用阿里云下的服务器。【*其它公共库*】
- 也有通过 Nexus 搭建的私服进行获取的。【*私服*】

**Maven 中的仓库分为两种，SNAPSHOT 快照仓库和 RELEASE 发布仓库。**

- SNAPSHOT 快照仓库用于保存开发过程中的不稳定版本，RELEASE 正式仓库则是用来保存稳定的发行版本。定义一个组件/模块为快照版本，只需要在 `pom` 文件中在该模块的版本号后加上 `-SNAPSHOT` 即可(注意这里必须是大写)，如下：

  ```xml
  <groupId>cc.mzone</groupId>
  <artifactId>m1</artifactId>
  <version>0.1-SNAPSHOT</version>
  <packaging>jar</packaging>
  ```

- Maven 会根据模块的版本号(`pom` 文件中的 `version`)中是否带有 `-SNAPSHOT` 来判断是快照版本还是正式版本。

  - 如果是快照版本，那么在 `mvn deploy` 时会自动发布到快照版本库中，会覆盖老的快照版本。而在使用快照版本的模块，在不更改版本号的情况下，直接编译打包时，Maven 会自动从镜像服务器上下载最新的快照版本。
  - 如果是正式发布版本，那么在 `mvn deploy` 时会自动发布到正式版本库中，而使用正式版本的模块，在不更改版本号的情况下，编译打包时如果本地已经存在该版本的模块则不会主动去镜像服务器上下载。

在开发阶段，可以将公用库的版本设置为快照版本，而被依赖组件则引用快照版本进行开发，在公用库的快照版本更新后，我们也不需要修改 pom 文件提示版本号来下载新的版本，直接 mvn 执行相关编译、打包命令即可重新下载最新的快照库了，从而也方便了我们进行开发。

**什么是私服？**

私服是一种特殊的远程仓库，它是架设在局域网内的仓库服务，私服代理广域网上的远程仓库，供局域网内的 Maven 用户使用。当 Maven 需要下载构件的时候，它从私服请求，如果私服上不存在该构件，则从外部的远程仓库下载，缓存在私服上之后，再为 Maven 的下载请求提供服务。我们还可以把一些无法从外部仓库下载到的构件上传到私服上。

Maven 私服的 5 个特性：

- 1、节省自己的外网带宽：减少重复请求造成的外网带宽消耗。
- 2、加速 Maven 构件：如果项目配置了很多外部远程仓库的时候，构建速度就会大大降低。
- 3、部署第三方构件：有些构件无法从外部仓库获得的时候，我们可以把这些构件部署到内部仓库(私服)中，供内部 Maven 项目使用。
- 4、提高稳定性，增强控制：Internet 不稳定的时候，Maven 构建也会变的不稳定，一些私服软件还提供了其他的功能。
- 5、降低中央仓库的负荷：Maven 中央仓库被请求的数量是巨大的，配置私服也可以大大降低中央仓库的压力。

当前主流的 Maven 私服：

- Apache 的 Archiva
- JFrog 的 Artifactory
- 【主流】Sonatype 的 Nexus 。

常见的 Maven 私服的仓库类型???：

- （宿主仓库）hosted repository 。
- （代理仓库）proxy repository 。
- （仓库组）group repository 。

对于大多数公司，一般来说使用 Nexus + 阿里云仓库的方式，可参考如下两篇文章：

- [《使用 Nexus 搭建 Maven 私有仓库》](https://www.jianshu.com/p/9740778b154f)
- [《Nexus 配置阿里云仓库》](https://blog.csdn.net/qq_30633989/article/details/80399596)

**如何配置远程仓库？**

用文本编辑器工具打开 `setting.xml` 文件，增加一个 `<mirror />`：

```xml
<mirrors>
    <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>*</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
</mirrors>
```