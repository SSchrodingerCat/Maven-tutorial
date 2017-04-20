Maven介绍及其使用
===================
----------


## Maven简介 ##
Maven是一个可以用来构建和管理任何基于java编程语言的项目的工具，可以让每天日复一日工作的java开发者更加简单的开发java项目。
Maven的主要目标是允许开发人员在最短的时间内了解开发工作的完整状态。为了实现这一目标，Maven试图处理以下几个方面的问题：

> - 使构建过程容易
> - 提供统一的构建系统
> - 提供优质的项目信息
> - 提供最佳做法发展指南
> - 允许透明迁移到新功能

[来源于apache maven官方介绍文档](https://maven.apache.org/what-is-maven.html)

----------
## Maven安装及配置 ##
### linux系统 ###

 1. 进入maven官方万站下载界面查找最新版本的maven下载地址[maven官方下载地址](https://maven.apache.org/download.cgi)
 2. 以maven3.5.0版本的maven作为例子，在linux shell界面输入如下指令

```
wget http://mirror.bit.edu.cn/apache/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.tar.gz
```

 会在当前目录下载maven源码
 
 3. 解压压缩包，指令如下:

``` 
tar -zxvf apache-maven-3.5.0-bin.tar.gz
```

 4. 设置环境变量（此步骤略）
 5. 验证是否成功，输入如下命令：
 

```
mvn -version
```

 如出现以下信息，则安装成功

```
Apache Maven 3.5.0 (ff8f5e7444045639af65f6095c62210b5713f426; 2017-04-04T03:39:06+08:00)
Maven home: /home/xieyuan/apache-maven-3.5.0
Java version: 1.8.0_121, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.121-0.b13.el7_3.x86_64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "3.10.0-514.10.2.el7.x86_64", arch: "amd64", family: "unix"
```
----------
## Maven核心概念介绍 ##
#### **pom**
pom指project object model，一般使用pom.xml来体现，是maven工作的基本部件。可以将pom.xml文件简单理解成项目的配置文件。pom.xml包含了maven使用和生成项目的配置的信息，譬如说生成的目录为`target`，java文件存放在`src/main/java`，测试java文件存放在`src/rest/java`等。pom.xml替代了maven.xml，所有的插件都配置在pom.xml中，当要执行一个任务或者目标时，maven扫描当前项目的pom文件，获得需要的配置信息，并执行目标。
*__Super POM__*
超级pom文件是maven的默认pom，所有的pom都会继承这个pom文件除非明确的设置。超级pom中所有的配置都会被内置到你创建的项目的pom文件中，以下是maven2.0.x的超级pom文件。

```Apache
<project>
  <modelVersion>4.0.0</modelVersion>
  <name>Maven Default Project</name>
 
  <repositories>
    <repository>
      <id>central</id>
      <name>Maven Repository Switchboard</name>
      <layout>default</layout>
      <url>http://repo1.maven.org/maven2</url>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>
 
  <pluginRepositories>
    <pluginRepository>
      <id>central</id>
      <name>Maven Plugin Repository</name>
      <url>http://repo1.maven.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
      <releases>
        <updatePolicy>never</updatePolicy>
      </releases>
    </pluginRepository>
  </pluginRepositories>
 
  <build>
    <directory>target</directory>
    <outputDirectory>target/classes</outputDirectory>
    <finalName>${artifactId}-${version}</finalName>
    <testOutputDirectory>target/test-classes</testOutputDirectory>
    <sourceDirectory>src/main/java</sourceDirectory>
    <scriptSourceDirectory>src/main/scripts</scriptSourceDirectory>
    <testSourceDirectory>src/test/java</testSourceDirectory>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
      </resource>
    </resources>
    <testResources>
      <testResource>
        <directory>src/test/resources</directory>
      </testResource>
    </testResources>
  </build>
 
  <reporting>
    <outputDirectory>target/site</outputDirectory>
  </reporting>
 
  <profiles>
    <profile>
      <id>release-profile</id>
 
      <activation>
        <property>
          <name>performRelease</name>
        </property>
      </activation>
 
      <build>
        <plugins>
          <plugin>
            <inherited>true</inherited>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
 
            <executions>
              <execution>
                <id>attach-sources</id>
                <goals>
                  <goal>jar</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <inherited>true</inherited>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-javadoc-plugin</artifactId>
 
            <executions>
              <execution>
                <id>attach-javadocs</id>
                <goals>
                  <goal>jar</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <inherited>true</inherited>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-deploy-plugin</artifactId>
 
            <configuration>
              <updateReleaseInfo>true</updateReleaseInfo>
            </configuration>
          </plugin>
        </plugins>
      </build>
    </profile>
  </profiles>
</project>
```
由此可见，maven的[repository](#user-content-Repositories)在[http://repo1.maven.org/maven2](http://repo1.maven.org/maven2)，可以方便的在[mvn repo](https://mvnrepository.com/)找到自己所需的repo。这个pom文件还包括了build目标等基本配置。
*__Minimal POM__*
最小的pom文件必须包含以下四点：
> - project root tag `<project>`
> - 模块的版本`<modelVersion>`
> - 公司id，一般以公司域名的倒叙填写 `<groupId>`
> - 模块id，必须和groupId组成一个唯一标识，以便上传至共有repo `<artifactId>`
> - 版本号 `<version>`
以下是最小pom文件的例子：
```
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```
其他未定义的配置将会使用super pom中的配置。

*__Project Inheritance__*

在pom.xml内置到其他pom.xml文件的过程中，以下在pom中的元素会被合并：
> - dependencies (依赖库)
> - developers and contributors
> - plugin lists(including reports)(插件列表)
> - plugin executions with matching ids(匹配的插件执行tag)
> -plugin configuration(插件配置)
> - resources(资源)

*__Example__*

将parent pom配置加入到child pom中：
当pom的目录结构为以下情况时，可以使用如下pom添加
```
.
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```
```
<project>
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```
当pom的目录结构为以下情况时，可以使用如下pom添加，注意使用了relativePath标签：
```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```
```
<project>
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  <artifactId>my-module</artifactId>
</project>
```

*__Project Aggregation__*

这个很类似于inheritance，但是他会优先执行parent pom的配置。

*__Project Interpolation and Variables__*

pom.xml也可使用变量配置，譬如`<version>${project.version}</version>`需要注意的是变量的值由最后一个定义了他的child pom决定。

[来源于apache maven pom官方介绍文档](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)，如有错误，希望大家联系我。

#### **Repositories**

*__repository__*

仓库是maven存储依赖库和已经生成的模块的地方。简而言之，repo存储了各位java前辈制造的轮子，我们可以通过repo下载我们想要的各种依赖，比如说spring框架API。
严格来说。只有两种repo：本地的和远程的。本地的repo有从远程repo上下载的依赖的副本，当然还有你没有发布的项目临时副本。远程repo一般指那些可信第三方提供的下载依赖的地方。

[来源于apache maven repo官方介绍文档](https://maven.apache.org/guides/introduction/introduction-to-repositories.html)，如有错误，希望大家联系我。

#### **Build Lifecycle**

*__Build Lifecycle Basics__*

maven的中心概念是基于生成周期的，这意味着生成一个特别的项目的过程是被详细定义的。
一个开发者生成一个项目只需要学习很少的maven命令，pom文件会保证开发者得到他想要的结果。
有三个内置的生存周期：**default**，**site**，**clean**，**default**处理你项目的部署，**clean**处理项目的清理，**site**处理生成设置文档。
*__A Build Lifecycle is Made Up of Phases__*
每一个生成周期都由不同的生成时期组成，每一个生成时期代表了生成周期的一个阶段。
举个例子来说，**default**由以下的时期组成（完整的生成时期，请参照[Lifecycle Reference](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference)）。
> - `validate` - 验证项目是否存在语法错误并且是否所有必要信息都存在。
> - `compile` - 编译源代码
> - `test` - 使用合适的测试框架测试编译的源代码，这些测试不能使用被打包的或者被发布的代码。
> - `package` - 使用规定的格式打包编译后的源代码，譬如说JAR格式。
> - `verify` - 运行所有结果集成测试以确保满足质量标准。
> - `install` - 将项目下载到本地ropo，用作其他项目的依赖。
> - `deploy` -完成生成环境，将最终包的副本发送到远程repo中。

*__Usual Command Line Calls__*

在开发者环境中，可以使用命令行执行命令。譬如说：
```
mvn install
```
这个命令执行所有在**default**生成周期中install及其之前的所有生成时期的任务。
也可以多个组合生成时期，像这样：
```
mvn clean deploy
```

*__A Build Phase is Made Up of Plugin Goals__*

每一个生成时期又是由多个插件目标组成，一个插件目标代表了一项特定的任务去生成和管理项目。插件目标可能绑定在0个或多个生成时期上，对于没有绑定在任何生成时期的插件目标可以在生成周期之外单独执行。执行插件目标的顺序依靠哪些插件目标和生成时期被绑定的顺序，举例来说，以下命令中**clean**和**package**为生成时期，**dependency:copy-dependencies**为插件目标。
```
mvn clean dependency:copy-dependencies package
```
执行顺序依次是clean->dependency:copy-dependencies->package
*__Some Phases Are Not Usually Called From the Command Line__*
以**pre，post，process**指令是不会被经常直接调用的。
*__Setting Up Your Project to Use the Build Lifecycle__*
基本的lifecycle简单实用，下面介绍如何使用它们。

 1. Packaging
 打包是最常用的方法组装新的lifestyle，通过在pom中新`<packaging>`标签就可以完成，一些可见的packaging value为**jar，war，ear，pom**如果未指定packaging value，将会默认为**jar**。
 每一个包都包含了一系列的目标去绑定特定的生成时期，譬如说，使用jar打包会绑定**default lifecycle**。
 2. 插件
第二种方式是使用插件，插件是给maven提供目标的项目。一个插件可能有一个或多个目标，比如说，**Compiler plugin**就包含两个目标：**compile，testCompile**。但是给一个项目添加插件并不能提供足够的信息，你必须指定你想要在生成时运行的目标。
配置的目标将会绑定在选择的packaging中,如果有多个goal绑定在同一个时期上，执行的顺序是先执行pckaging，再执行POM的配置。你可以使用`<executions>`标签获得更多的关于特定goals的控制信息。
举一个例子，插件Medello默认被绑定在goal`<modello:java>` `<generate-sources>`时期。所以使用Modello插件,它从模型生成消息,并将其集成到构建。添加插件语法：
```
<project>
...
	 <plugin>
	   <groupId>org.codehaus.modello</groupId>
	   <artifactId>modello-maven-plugin</artifactId>
	   <version>1.8.1</version>
	   <executions>
	     <execution>
	       <configuration>
	         <models>
	           <model>src/main/mdo/maven.mdo</model>
	         </models>
	         <version>4.0.0</version>
	       </configuration>
	       <goals>
	         <goal>java</goal>
	       </goals>
	     </execution>
	   </executions>
	 </plugin>
...
</project>
```

[来源于apache maven pom官方介绍文档](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)，如有错误，希望大家联系我。

#### **Archetype**
简单来说，Archetype就是maven的项目模板工具，Archetype被定义成最初项目的原型。

[来源于apache maven pom官方介绍文档](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)，如有错误，希望大家联系我

----------
## Maven使用 ##

 1. 创建工程
创建项目骨架
```
mvn archetype:generate
```
或者(maven 2用户推荐)
```
mvn org.apache.maven.plugins:maven-archetype-plugin:2.0-alpha-5:generate
```
如果是第一次使用，mvn会在远程repo中下载archetype，要等待一定时间，以网络情况而定。紧接着我们会看到一段长长的输出，有很多可用的archetype供我们选择。包括著名的Appfuse项目的archetype，JPA项目的archetype等等。每一个archetype前面都会对应有一个编号，同时命令行会提示一个默认的编号，其对应的archetype为maven-archetype-quickstart，我们直接回车以选择该archetype，紧接着Maven会提示我们输入要创建项目的groupId、artifactId、 version、以及包名package。


