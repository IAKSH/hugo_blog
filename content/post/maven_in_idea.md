---
title: "Maven_in_idea"
date: 2024-02-29T14:22:38Z
draft: true
summary: "IDEA中Maven的基本使用"
---

# 0x00 | 总述
`Maven`实际上为`Java`开发者提供了一套包管理器和构建系统，类似于`C++`的`vcpkg`+`cmake`+`make/ninja`。

简单地说，`Maven`能够自动完成包括但不限于下载并配置第三方包以及编译和打包项目的任务。

`Maven`同时还支持插件，在插件的帮助下，`Maven`甚至能自动化完成编译，测试，再到部署的整个过程。

`Maven`通过`pom.xml`描述一个项目的基本信息以及依赖项，同时也包括了对`Maven插件`的配置。在更新`pom.xml`时，`Maven`将会尝试自动下载并配置项目的各个依赖项。

`IDEA`内部集成了`Maven3`，在设置中也能使用外部的`Maven`。本文主要介绍对`pom.xml`文件的配置以及如何在`IDEA`中使用`Maven`编译，测试以及打包项目。

# 0x01 | 创建一个Maven工程
在`IDEA`的”新建项目“对话框中，可以选择构建系统为`Maven`。或者可以从左侧“生成器”中手动选择`Maven Archetype`此处的`Archetype`选项中有一些常用的预设方案。 

# 0x02 | Maven菜单
创建好项目并打开后，在界面的右侧有一个`m`字样图标，这是`maven`的菜单栏。其中展示了当前工程中的所有`Maven项目`的”生命周期“以及”插件“。在前者中你能找到各种常用的指令。

`IDEA`本身的编译，运行以及调试都能正常运作。但是`Maven`提供了更为丰富的功能，比如自动运行基于`junit`的单元测试，自动打包项目为`jar`。
# 0x03 | pom.xml
`IDEA`在创建`Maven`项目时，会自动生成一份`pom.xml`在项目的根目录。除了`groupId`，`artifactId`以及`version`外，该文件主要有一下几个常用的部分。

另外，当你修改`pom.xml`后，编辑器右侧将会出现加载`Maven`修改的按钮。

## 1. properties
这一段应该是自动生成的，主要是指定项目所使用的`Java`版本以及源文件编码。
```xml
<properties>  
    <maven.compiler.source>17</maven.compiler.source>  
    <maven.compiler.target>17</maven.compiler.target>  
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>  
</properties>
```
## 2. dependencie
这里就是填写项目依赖项的地方，下面是关于此块的一个示例：
```xml
<dependencies>  
    <!-- https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client -->  
    <dependency>  
        <groupId>org.mariadb.jdbc</groupId>  
        <artifactId>mariadb-java-client</artifactId>  
        <version>3.2.0</version>  
    </dependency>    <!-- 
    </dependency>    <!-- https://mvnrepository.com/artifact/junit/junit -->  
    <dependency>  
        <groupId>junit</groupId>  
        <artifactId>junit</artifactId>  
        <version>4.13.2</version>  
	</dependency>
</dependencies>
```
你可以在[这里](https://mvnrepository.com)寻找你需要的包。

## 3. build
这一段主要是通过添加插件来完成项目的打包。默认的不设置此项的情况下，`Maven`也能打包项目到`.jar`，但是由于未指定主类，并不能运行。而且，没有格外配置的`Maven`只会打包你的代码，并不会包含依赖。
下面是一个配置主类并且自动将依赖加入`.jar`中的示例：
```xml
<build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.2.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <mainClass>me.iaksh.api.Main</mainClass> <!-- 此处为主入口-->
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <configuration>
                    <descriptorRefs>
                        <!--给jar包起的别名-->
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                            <!--添加项目中主类-->
                            <mainClass>me.iaksh.api.Main</mainClass>
                        </manifest>
                    </archive>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```