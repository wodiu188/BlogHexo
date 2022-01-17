---
title: Maven
date: 2022-01-14 18:38:35
tags:
      - Maven
      - Java
      - 打包工具
categories: 
description: 哎呦，事隔多年终于把这块整理出来了。老早就想学了，但是本着能凑合用的原则一直没捡起来在这感谢C语言中文网
keywords:
      - 打包工具
      - Maven
      - mvn

---

# Maven

介绍：

是一个用于管理Java项目的包管理工具



Maven 能够帮助开发者完成以下任务：

- 构建项目
- 生成文档
- 创建报告
- 维护依赖
- 软件配置管理
- 发布
- 部署

然后默认情况下maven项目是这样的

| 文件         | 目录               |
| ------------ | ------------------ |
| Java 源代码  | src/main/java      |
| 资源文件     | src/main/resources |
| 测试源代码   | src/test/java      |
| 测试资源文件 | src/test/resources |
| 打包输出文件 | target             |
| 编译输出文件 | target/classes     |

 

emmm，maven的下载安装我就不讲了



### POM文件



该文件类型一般是xml

一般包含如下内容:

- 项目依赖
- 插件
- 目标
- 构建时的配置文件
- 版本 
- 开发者
- 邮件列表

> 在创建 POM 之前，首先要确定工程组（groupId），及其名称（artifactId）和版本，在仓库中这些属性是项目的唯一标识。

一般的文件内容模板是

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>net.biancheng.www</groupId>
    <artifactId>maven</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</project>
```

| 节点       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| groupId    | 项目组 ID，定义当前 Maven 项目隶属的组织或公司，通常是唯一的。它的取值一般是项目所属公司或组织的网址或 URL 的反写，例如 net.biancheng.www。 |
| artifactId | 项目 ID，通常是项目的名称。groupId 和 artifactId 一起定义了项目在仓库中的位置。 |
| version    | 项目版本。                                                   |

所有的 POM 均继承自一个父 POM，这个父 POM 被称为 Super POM,如果想要查看Super POM的默认配置使用下面这个命令:

> mvn help:effective-pom

太长了就不粘贴了....



#### 创建 Maven 项目

我们通过模板来创建一个maven项目

首先cd到你要创建项目的目录

> mvn archetype:generate -DgroupId=net.biancheng.www -DartifactId=helloMaven -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false



上面这个是使用maven-archetype-quickstart模板来快速创建Maven

就相当于在idea中这么创建模板

![image-20220114192838385](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20220114192838385.png)



- -DgroupId: 项目组 ID，通常为组织名或公司网址的反写。
- -DartifactId: 项目名。
- -DarchetypeArtifactId: 指定 ArchetypeId,maven-archetype-quickstart 用于快速创建一个简单的 Maven 项目。
- -DinteractiveMode: 是否使用交互模式。



以上面的项目为例子创建完成后目录结构大致如下：

![image-20220114193503942](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-2.png)



#### 构建项目

使用如下命令进行构建

>mvn clean package

会生成一个target目录,而target目录如下

![image-20220114193818618](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-3.png)





- classes：源代码编译后存放在该目录中。

  - 在这个目录下面命令即可开始运行

  - > java net.biancheng.www.App(这个就是开始那个构建的域名加上.App)

    

- test-classes：测试源代码编译后并存放在该目录中。

- surefire-reports：Maven 运行测试用例生成的测试报告存放在该目录中。

- helloMaven-1.0-SNAPSHOT.jar：Maven 对项目进行打包生成的 jar 文件。



==maven是通过groupId,artifactId,version,packaging的来具体定位的==



#### 依赖导入

如果我们要导入依赖时必须按照下面的格式

```xml
    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
```

这样maven会根据坐标来自动导入到项目中

根据组织名称(groupId),项目名称(artifactId),版本名称(version),type(依赖类型,大多情况不用管默认是jar)

- scope：依赖的范围。
- optional：标记依赖是否可选。
- exclusions：用来排除传递性依赖。

至于从哪里找依赖,需要从下面这个链接

https://mvnrepository.com/



#### 仓库介绍

仓库分为本地仓库和远程仓库,如果我们定位依赖的时候会现在本地仓库搜索然后再去远程仓库搜索.



远程仓库又分为下面几个

- 中央仓库是由 Maven 社区提供的一种特殊的远程仓库，它包含了绝大多数流行的开源构件。在默认情况下，当本地仓库没有 Maven 所需的构件时，会首先尝试从中央仓库下载。
- 私服是一种特殊的远程仓库，它通常设立在局域网内，用来代理所有外部的远程仓库。它的好处是可以节省带宽，比外部的远程仓库更加稳定。 
- 除了中央仓库和私服外，还有很多其他公共仓库，例如 JBoss Maven 库，Java.net Maven 库等等。

本地仓库的设置一般在Maven下的conf/settings.xml

这个文件下面的

> 1. **<settings** xmlns="http://maven.apache.org/SETTINGS/1.0.0"
> 2. ​          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
> 3. ​          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
> 4.    http://maven.apache.org/xsd/settings-1.0.0.xsd"**>**
> 5. ​    **<localRepository>**D:/myRepository/repository**</localRepository>**
> 6. **</settings>**



#### 生命周期

- clean：用于清理项目
  - 又可以分为如下三个阶段
  - pre-clean（清理前）
  - clean（清理）
  - post-clean（清理后）
- default：用于构建项目
  - 包含如下声明周期
  - ![image-20220114201141085](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-4.png)
- site：用于建立项目站点
  - 这个站点的项目构建步骤是下面这种
  - pre-site
  - site
  - post-site
  - site-deploy

#### Maven插件

maven在上面的生命周期的每个任务不是maven来实现的而是用插件来实现的,maven本身不包含插件只有需要的时候才会去下载.



| 插件类型          | 描述                                                       |
| ----------------- | ---------------------------------------------------------- |
| Build plugins     | 在项目构建过程中执行，在 pom.xml 中的 build 元素中配置     |
| Reporting plugins | 在网站生成过程中执行，在 pom.xml 中的 reporting 元素中配置 |

例如如下部分:

![image-20220114202137641](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-5.png)





自定义插件绑定

```xml
<project>
...
    <build>
        <plugins>
            <!-- 绑定插件 maven-antrun-plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-antrun-plugin</artifactId>
                <version>1.8</version>
                <executions>
                    <execution>
                        <!--自定义 id -->
                        <id>www.biancheng.net clean</id>
                        <!--插件目标绑定的构建阶段 -->
                        <phase>clean</phase>
                        <!--插件目标 -->
                        <goals>
                            <goal>run</goal>
                        </goals>
                        <!--配置 -->
                        <configuration>
                            <!-- 执行的任务 -->
                            <tasks>
                                <!--自定义文本信息 -->
                                <echo>清理阶段，编程帮 欢迎您的到来，网址：www.biancheng.net</echo>
                            </tasks>
                        </configuration>
                    </execution>               
                </executions>
            </plugin>
        </plugins>
    </build>
...
</project>
```

其中execution下的各个含义是:

- id：任务的唯一标识。
- phase：插件目标需要绑定的生命周期阶段。
- goals：用于指定一组插件目标，其子元素 goal 用于指定一个插件目标。
- configuration：该任务的配置，其子元素 tasks 用于指定该插件目标执行的任务。



#### 导入本地

如果我们要导入以前的maven项目就需要先将以前的项目打成一个jar包

使用mvn clean package包

然后再在需要该项目的pom.xml的dependencies中插入以下数据

```xml
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <!--外部依赖-->
        <dependency>
            <groupId>net.biancheng.www</groupId>
            <artifactId>helloMaven</artifactId>
             <!--依赖范围-->
            <scope>system</scope>
            <version>1.0-SNAPSHOT</version>
            <!--依赖所在位置-->
            <systemPath>D:\maven\helloMaven\target\helloMaven-1.0-SNAPSHOT.jar</systemPath>
        </dependency>
    </dependencies>
```

如果有了项目但是不知道groupId，artifactId在哪看可以在jar包中查看

![image-20220115090557928](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-9.png)







其中<systemPath>是指代jar包的路径

然后执行下面的命令进行编译

> mvn clean compile

然后使用下面命令来设置临时的环境变量

```bash
set classpath=%classpath%;D:\maven\helloMaven\target\helloMaven-1.0-SNAPSHOT.jar
```

然后使用java 组织名App运行







然后idea版本的

首先File->Project Structure

![image-20220114204612350](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-6.png)

然后Liberaies的add

![image-20220114204711280](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-7.png)



#### Archetype(Maven的模板)

模板一般由下面这几部分构成

- maven-archetype-plugin：Archetype 插件。
- archetype-packaging：用于描述 Archetype 的生命周期与构建项目软件包。
- archetype-models：用于描述类与引用。
- archetype-common：核心类。
- archetype-testing：用于测试 Maven Archetype 的内部组件。



==众所周知!!!!==maven的功能都是用插件来实现的

他是由maven-archetype-plugin 的插件实现的来实现的



#### SNAPSHOT和RELEASE

有的时候一个项目可能由不同的部门来负责不同的部分,但是如果有一个部门更新换代很快那么就要经常更换maven依赖,为了方便解决我们可以使用SNAPSHOT

定义一个组件或模块为快照版本，只需要在其 pom.xml 中版本号（version 元素的值）后加上 -SNAPSHOT 即可，例如：

```xml

<groupId>net.biancheng.www</groupId>
<artifactId>helloMaven</artifactId>
<packaging>jar</packaging>
<version>1.0-SNAPSHOT</version>
```

这样他不用重复的修改pox.xml文件了,会自动更新代码库

> 默认maven会每天更新一次库如果要强行更新请使用下面的命令
>
> mvn clean package -U



SNAPSHOT和RELEASE的区别是

前者是属于不稳定版本,后者是属于稳定的发布版本

| 区别                       | SNAPSHOT 版本                                                | RELEASE 版本                                                 |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义                       | 版本号中带有 -SNAPSHOT                                       | 版本号中不带有 -SNAPSHOT                                     |
| 发布仓库                   | Snapshot 快照仓库                                            | Release 发行仓库                                             |
| 是否从远程仓库自动获取更新 | 在不更改版本号的前提下，直接编译打包时，Maven 会自动从远程仓库上下载最新的快照版本。 | 在不更改版本号的前提下，直接编译打包时，如果本地仓库已经存在该版本的模块，则 Maven 不会主动去远程仓库下载。 |
| 稳定性                     | 快照版本往往对应了大量带有时间戳的构件，具有不稳定性。       | 发布版本只对应了唯一的构件，具有稳定性。                     |
| 使用场景                   | 快照版本只应该在组织内部的项目中依赖使用。                   | Maven 项目使用的组织外的依赖项都应该时发布版本的，不应该使用任何的快照版本依赖，否则会造成潜在的风险。 |
| 发布前是否需要修改         | 当项目经过完善的测试后，需要上线时，应该将项目从快照版本更改为发布版本 | 不需要修改                                                   |



#### 自动打包插件

```xml
<plugins>
    <!-- 添加invoker 插件 -->
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-invoker-plugin</artifactId>
        <version>3.2.2</version>
        <configuration>
            <debug>true</debug>
            <!--设置 invkoer插件 添加的 pom 文件所在的文件夹  -->
            <projectsDirectory>D:\maven</projectsDirectory>
            <!-- 设置 invkoer插件 添加的 pom 文件 -->
            <pomIncludes>
                <pomInclude>secondMaven\pom.xml</pomInclude>
                <pomInclude>thirdMaven\pom.xml</pomInclude>
            </pomIncludes>
        </configuration>
        <executions>
            <execution>
                <id>id-integration-test</id>
                <!-- 执行的目标 -->
                <goals>
                    <goal>run</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
</plugins>
```

这是自动打包插件,可以将多个maven项目一起进行打包,下图是所用到的项目

![image-20220115085148870](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-8.png)



> mvn help:describe -Dplugin=invoker 使用打包软件进行打包

```bash

Name: Apache Maven Invoker Plugin
Description: The Maven Invoker Plugin is used to run a set of Maven projects.
  The plugin can determine whether each project execution is successful, and
  optionally can verify the output generated from a given project execution.
Group Id: org.apache.maven.plugins
Artifact Id: maven-invoker-plugin
Version: 3.2.2
Goal Prefix: invoker

This plugin has 6 goals:

invoker:help
  Description: Display help information on maven-invoker-plugin.
    Call mvn invoker:help -Ddetail=true -Dgoal=<goal-name> to display parameter
    details.

invoker:install
  Description: Installs the project artifacts of the main build into the
    local repository as a preparation to run the sub projects. More precisely,
    all artifacts of the project itself, all its locally reachable parent POMs
    and all its dependencies from the reactor will be installed to the local
    repository.

invoker:integration-test
  Description: Searches for integration test Maven projects, and executes
    each, collecting a log in the project directory, will never fail the build,
    designed to be used in conjunction with the verify mojo.

invoker:report
  Description: Generate a report based on the results of the Maven
    invocations. Note: This mojo doesn't fork any lifecycle, if you have a
    clean working copy, you have to use a command like mvn clean
    integration-test site to ensure the build results are present when this
    goal is invoked.
  Note: This goal should be used as a Maven report.

invoker:run
  Description: Searches for integration test Maven projects, and executes
    each, collecting a log in the project directory, and outputting the results
    to the command line.

invoker:verify
  Description: Checks the results of maven-invoker-plugin based integration
    tests and fails the build if any tests failed.

For more information, run 'mvn help:describe [...] -Ddetail'

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  02:53 min
[INFO] Finished at: 2022-01-15T09:16:27+08:00
[INFO] ------------------------------------------------------------------------

Process finished with exit code 0

```

可以看到上面这些成功信息





#### 依赖排除和可选依赖

当有A依赖B，B依赖Y和X，但是Y和X互斥我们就需要排除一个依赖了

```xml
    <dependencies>
        <dependency>
            <groupId>net.biancheng.www</groupId>
            <artifactId>B</artifactId>
            <version>1.0-SNAPSHOT</version>
            <exclusions>
                <!-- 设置排除 -->
                <!-- 排除依赖必须基于直接依赖中的间接依赖设置为可以依赖为 false -->
                <!-- 设置当前依赖中是否使用间接依赖 -->
                <exclusion>
                    <!--设置具体排除-->
                    <groupId>net.biancheng.www</groupId>
                    <artifactId>X</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

在需要排除的依赖里面添加(B里面的X进行一波排除),无需指定版本



下面是可选依赖

```xml
<dependencies>
        <dependency>
            <groupId>net.biancheng.www</groupId>
            <artifactId>X</artifactId>
            <version>1.0-SNAPSHOT</version>
            <!--设置可选依赖  -->
            <optional>true</optional>
        </dependency>
    </dependencies>
```

这是optional为true则代表不可以被继承下去



#### 继承

这个我在idea的时候经常遇见,就是我创建一个maven项目后,又在该项目下继续创建一个maven项目他就会自动把外层项目当成父项目



结果如下

![image-20220116090705000](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-10.png)





![image-20220116095724720](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-11.png)

可以看出他继承了父依赖



下面是可以继承的pom参数

| 元素                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| groupId                | 项目组 ID，项目坐标的核心元素                                |
| version                | 项目版本，项目坐标的核心元素                                 |
| description            | 项目的描述信息                                               |
| organization           | 项目的组织信息                                               |
| inceptionYear          | 项目的创始年份                                               |
| url                    | 项目的URL地址                                                |
| developers             | 项目的开发者信息                                             |
| contributors           | 项目的贡献者信息                                             |
| distributionManagement | 项目的部署配置                                               |
| issueManagement        | 项目的缺陷跟踪系统信息                                       |
| ciManagement           | 项目的持续集成系统信息                                       |
| scm                    | 项目的版本控制系统信息                                       |
| mailingLists           | 项目的邮件列表信息                                           |
| properties             | 自定义的Maven属性                                            |
| dependencies           | 项目的依赖配置                                               |
| dependencyManagement   | 项目的依赖管理配置                                           |
| repositories           | 项目的仓库配置                                               |
| build                  | 包括项目的源码目录配置、输出目录配置、插件配置、插件管理配置等 |
| reporting              | 包括项目的报告输出目录配置、报告插件配置等                   |



#### 默认依赖版本

使用dependencyManagement可以为包设置默认的版本

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <!--由于不是继承，所以必须重新添加 groupId 和 version-->
    <groupId>net.biancheng.www</groupId>
    <artifactId>App-Data-lib</artifactId>
    <version>1.0</version>

    <!--dependencyManagement 标签用于控制子模块的依赖版本等信息 -->
    <!-- 该标签只用来控制版本，不能将依赖引入 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>1.2.17</version>
            </dependency>

            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>4.9</version>
                <!-- <scope>test</scope> -->
            </dependency>

            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>5.1.18</version>
                <scope>runtime</scope>
            </dependency>

            <dependency>
                <groupId>c3p0</groupId>
                <artifactId>c3p0</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>0.9.1</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!--声明依赖-->
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
</project>
```

这里junit在dependencyManagement的外部并没有指定版本就会使用dependencyManagement里面的版本



==如果子类继承的父类有dependencyManagement,则子类也可以享受默认版本的便利,不过dependencyManagement导入的依赖并不会生效,只有没被dependencyManagement包裹的dependencies才会生效==



在依赖范围中提过import,这个依赖范围只会在有dependencyManagement的时候才会生效,因为他的作用是将dependencyManagement复制到当前项目中,使用如下:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <!--由于不是继承，所以必须重新添加 groupId 和 version-->
    <groupId>net.biancheng.www</groupId>
    <artifactId>App-Data-lib</artifactId>
    <version>1.0</version>
    <!--定义依赖管理-->
    <dependencyManagement>
        <dependencies>
            <!--导入依赖管理配置-->
            <dependency>
                <groupId>net.biancheng.www</groupId>
                <artifactId>Root</artifactId>
                <version>1.0</version>
                <!--依赖范围为 import-->
                <scope>import</scope>
                <!--类型一般为pom-->
                <type>pom</type>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!--声明依赖-->
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
</project>
```



#### 聚合

如果你想要将本项目和某些模块一起打包的时候就可以使用聚合

```xml
    <!--添加需要聚合的模块-->
    <modules>
        <module>../App-Core-lib</module>
        <module>../App-Data-lib</module>
        <module>../App-UI-WAR</module>
    </modules>
```

> mvn clean install

使用上面这个命令进行打包



![Maven 聚合](D:\Code\pojo\Blog\BlogHexo\public\img\Maven-12.png)

从上图可以发现其他的模块也一起打包了



#### pluginManagement



#### Profile



#### 镜像



#### 私服



### Maven命令

- mvn help:effective-pom  查看Super POM的默认值

- mvn clean package  对项目进行打包构建

- mvn clean compile 对目标项目进行编译

- mvn install 构建阶段

- mvn [插件名]:[目标名] 

  - maven-compiler-plugin 插件的 compile 目标，命令如下

  - > mvn compiler:compile

- mvn site 生成项目的站点(让别人以网站的形式来了解这个项目,在target\site目录下)

- mvn archetype:generate 快速创建maven项目
  - 然后他会让你选择版本
  - 让你输入groupId,artifactId,version,package
  
- mvn help:describe -Dplugin=invoker 使用这个命令来进行自动打包

- mvn dependency:list 查看使用的依赖列表

