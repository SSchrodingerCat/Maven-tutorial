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
###linux系统###

 1. 进入maven官方万站下载界面查找最新版本的maven下载地址[maven官方下载地址](https://maven.apache.org/download.cgi)
 2. 以maven3.5.0版本的maven作为例子，在linux shell界面输入如下指令

```
wget http://mirror.bit.edu.cn/apache/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.tar.gz
```

 会在当前目录下载maven源码.
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
## Maven核心概念介绍##
####**pom**
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
这个很类似于inheritance，但是他会优先执行parent pom的配置
*__Project Interpolation and Variables__*
pom.xml也可使用变量配置，譬如`<version>${project.version}</version>`需要注意的是变量的值由最后一个定义了他的child pom决定。
[来源于apache maven pom官方介绍文档](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)，如有错误，希望大家联系我。
####**Repositories**
*__repository__*
仓库是maven存储依赖库和已经生成的模块的地方。简而言之，repo存储了各位java前辈制造的轮子，我们可以通过repo下载我们想要的各种依赖，比如说spring框架API。
严格来说。只有两种repo：本地的和远程的。本地的repo有从远程repo上下载的依赖的副本，当然还有你没有发布的项目临时副本。远程repo一般指那些可信第三方提供的下载依赖的地方。
[来源于apache maven repo官方介绍文档](https://maven.apache.org/guides/introduction/introduction-to-repositories.html)，如有错误，希望大家联系我。
####**Build Lifecycle**
####**Goal**
####**Archetype**
简单来说，Archetype就是maven的项目模板工具，Archetype被定义成最初项目的原型。

