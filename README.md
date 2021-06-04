# helloworld

## 使用第一个RESTful的Web服务

在java中访问https://quoters.apps.pcfone.io/api/random中的随机quotation。

### 模板：创建资源表示类

Spring 提供了一个方便的模板类，称为 RestTemplate。 RestTemplate 使与大多数 RESTful 服务的交互成，可以将该数据绑定到自定义类型。

```java
package com.weso.helloworld.consumingrest;

import com.fasterxml.jackson.annotation.JsonFormat.Value;
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
public class Quote {

	private String type;
	private Value value;

	public Quote() {
	}

	public String getType() {
		return type;
	}

	public void setType(String type) {
		this.type = type;
	}

	public Value getValue() {
		return value;
	}

	public void setValue(Value value) {
		this.value = value;
	}

	@Override
	public String toString() {
		return "Quote{" + "type='" + type + '\'' + ", value=" + value + '}';
	}
}

```

```java
package com.weso.helloworld.consumingrest;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
public class Value {

	private Long id;
	private String quote;

	public Value() {
	}

	public Long getId() {
		return this.id;
	}

	public String getQuote() {
		return this.quote;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public void setQuote(String quote) {
		this.quote = quote;
	}

	@Override
	public String toString() {
		return "Value{" + "id=" + id + ", quote='" + quote + '\'' + '}';
	}
}
```

其中用到了`@JsonIgnoreProperties`，表示任何未绑定在此类型中的属性都应被忽略。这里面用到的变量名需要和JSON里的键一一对应，否则需要用`@JsonProperty`进行指定。

### 控制：修改主类

为了显示从quotation服务中的到的内容，需要在主类中加入以下内容：

- logger：将输出发送到日志（在这个例子中是控制台）
- RestTemplate：它使用 Jackson JSON 处理库来处理传入的数据
- CommandLineRunner：在启动时运行 RestTemplate（并因此获取我们的报价）。

```java
package com.weso.helloworld;

import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import com.weso.helloworld.consumingrest.Quote;

@SpringBootApplication
@RestController
public class App {
	private static final org.slf4j.Logger log = LoggerFactory.getLogger(App.class);

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}

	@GetMapping("/")
	public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
		return String.format("Hello %s!", name);
	}

	@Bean
	public RestTemplate restTemplate(RestTemplateBuilder builder) {
		return builder.build();
	}

	@Bean
	public CommandLineRunner run(RestTemplate restTemplate) throws Exception {
		return args -> {
			Quote quote = restTemplate.getForObject("https://quoters.apps.pcfone.io/api/random", Quote.class);
			log.info(quote.toString());
		};
	}
}
```

运行后在控制台中显示`[main] com.weso.helloworld.App: Quote{type='success', value=Value{id=2, quote='With Boot you deploy everywhere you can find a JVM basically.'}}`
