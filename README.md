# helloworld

## 搭建第一个Spring Boot应用

在eclipse中新建一个maven project，使用默认配置，以下代码基于

- Spring Boot 2.5.0
- maven 3.3.9
- JDK8u151
- eclipse Oxygen for JEE

通过pom.xml中的标签指定版本。

### 配置：pol.xml

pom.xml 是用于构建项目的”配方“。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
    </parent>

    <!-- Additional lines to be added here... -->

</project>
```

spring-boot-starter-parent 是一个特殊的starter，提供有用的 Maven 默认值。 它还提供了一个依赖项管理部分，以便可以省略该版本spring boot包含的依赖项的版本标签。

此时在命令行中执行：

```bash
$ mvn dependency:tree

[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
```

可以发现spring-boot-starter-parent 并没有引入依赖。为了创建一个简单的web应用，我们引入以下依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

如果再次运行 mvn dependency:tree，会看到现在有许多额外的依赖项，包括 Tomcat Web 服务器和 Spring Boot 本身。

### 入口：SpringApplication

为了完成第一个应用，我们建一个入口类：

```java
package com.weso.helloworld;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Hello world!
 *
 */
@RestController
@EnableAutoConfiguration
public class App {

	@RequestMapping("/")
	String home() {
		return "Hello World!";
	}

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}

}
```

### 打包：jar

要打jar包需要在`pom.xml`中加入以下插件：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

在命令行执行`mvn package`，打包结果在`target`目录中，使用`java -jar`运行，使用`jar tvf`包内内容。





