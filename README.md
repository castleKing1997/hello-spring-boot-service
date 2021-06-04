# helloworld

## 搭建第一个RESTful的Web服务

通过访问ip:port/greeting?name=xxx返回JSON body`{"id":1,"content":"Hello, xxx!"}`。

### 模板：创建资源表示类

要对Greeting表示建模，创建一个资源表示类：

```java
package com.weso.helloworld.restservice;

public class Greeting {

	private final long id;
	private final String content;

	public Greeting(long id, String content) {
		this.id = id;
		this.content = content;
	}

	public long getId() {
		return id;
	}

	public String getContent() {
		return content;
	}
}
```

### 控制：创建资源控制器

```java
package com.weso.helloworld.restservice;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

	private static final String template = "Hello, %s!";
	private final AtomicLong counter = new AtomicLong();

	@GetMapping("/greeting")
	public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
		return new Greeting(counter.incrementAndGet(), String.format(template, name));
	}
}
```

其中需要说明以下的Notation：

- @GetMapping("/greeting")：回应向`/greeting`发出的GET请求，此外

  @PostMapping：for post

  @RequestMapping：for all；指定方法`@RequestMapping(method=xxx)`

- @RequestParam：将查询字符串参数` name `的值绑定到 `greeting() `方法的` name` 参数中。 如果请求中没有 `name` 参数，则使用 `World `的 `defaultValue`。

- @RestController：将类标记为控制器，其中每个方法都返回对象而不是视图。它是包含@Controller 和@ResponseBody 。

传统 MVC 控制器和前面展示的 RESTful Web 服务控制器之间的主要区别在于 HTTP 响应主体的创建方式。 此 RESTful Web 服务控制器不依赖于视图技术将问候数据在服务器端呈现为 HTML，而是**填充并返回一个 Greeting 对象。 对象数据将作为 JSON 直接写入 HTTP 响应。** 因为 Jackson 2 在类路径上，所以会自动选择 Spring 的 MappingJackson2HttpMessageConverter 将 Greeting 实例转换为 JSON。 

[^1]: [理解RESTful架构 - 阮一峰的网络日志 (ruanyifeng.com)](http://www.ruanyifeng.com/blog/2011/09/restful.html)

