参考链接：

https://zhuanlan.zhihu.com/p/32442516
https://blog.csdn.net/codehole_/article/details/100892274

[spark项目地址](https://github.com/perwendel/spark-kotlin)

## spark介绍

尝试过Python/Ruby/Nodejs/Golang语言开发的人往往难以适应Java Web框架，相对于这些语言提供的web框架来说，Java的Web框架显的过于笨重了。

那有没有一种看起来很轻量级的Java Web框架呢？当然有，本篇介绍的Spark框架就是其中之一。此Spark不是大数据用到的Spark，名字相同，纯属巧合，两者完全没有关联性。

作者坦言Spark框架的灵感源于Ruby的Sinatra微框架，正好赶上了Java8迟来的闭包，于是就诞生了看起来非常轻量级的Spark。另外Google牵头的kotlin又正被炒的火热，Spark与时俱进，很快就出了一个kotlin版本的Spark框架。

## 使用入门

1. 引入依赖

```xml
<dependency>
    <groupId>com.sparkjava</groupId>
    <artifactId>spark-core</artifactId>
    <version>2.7.1</version>
</dependency>
```

2. 编写Hello World

```java
import static spark.Spark.*;

public class HelloWorld {
    public static void main(String[] args) {
        get("/hello", (req, res) -> "Hello World");
    }
}
```

3. 访问网页

运行一下上面的代码，在浏览器里访问http://localhost:4567/hello就可访问

## Sinatra框架与spark

```SAS
require 'sinatra'

get '/' do
  'Hello world!'
end
```

虽然Spark要输入的内容更多，但它们已经很接近了。值得一提的是，Spark框架启动速度非常快速，肉眼几乎没有延迟，而相比之下，SpringBoot的启动效率就之于马车和火箭的关系了。

也许你会当心Spark框架并不主流，估计不是太稳定吧。关于这一点我必须说明的是Spark本身只是底层Jetty内核容器的一个包装，Jetty才是Spark的灵魂，Spark不过是一间非常漂亮的外衣。

## Spark进阶

### Spark最常用的CRUD调用形式

```java
get("/", (request, response) -> {
    // Get Something
});

post("/", (request, response) -> {
    // Create something
});

put("/", (request, response) -> {
    // Update something
});

delete("/", (request, response) -> {
    // Delete something
});
```

### 设置web根路径

```java
staticFiles.location("/public");
```

### 路由组合

组合路由可以将页面API模块化

```java
path("/api", () -> {
    path("/email", () -> {
        post("/add",       EmailApi.addEmail);
        put("/change",     EmailApi.changeEmail);
        delete("/remove",  EmailApi.deleteEmail);
    });
    path("/username", () -> {
        post("/add",       UserApi.addUsername);
        put("/change",     UserApi.changeUsername);
        delete("/remove",  UserApi.deleteUsername);
    });
});
```


同时还支持过滤器，我们可以在过滤器里加鉴权逻辑/访问日志/服务耗时跟踪等

### request和response对象的API

```java
request.attributes();             // the attributes list
request.attribute("foo");         // value of foo attribute
request.attribute("A", "V");      // sets value of attribute A to V
request.body();                   // request body sent by the client
request.bodyAsBytes();            // request body as bytes
request.contentLength();          // length of request body
request.contentType();            // content type of request.body
request.contextPath();            // the context path, e.g. "/hello"
request.cookies();                // request cookies sent by the client
request.headers();                // the HTTP header list
request.headers("BAR");           // value of BAR header
request.host();                   // the host, e.g. "example.com"
request.ip();                     // client IP address
request.params("foo");            // value of foo path parameter
request.params();                 // map with all parameters
request.pathInfo();               // the path info
request.port();                   // the server port
request.protocol();               // the protocol, e.g. HTTP/1.1
request.queryMap();               // the query map
request.queryMap("foo");          // query map for a certain parameter
request.queryParams();            // the query param list
request.queryParams("FOO");       // value of FOO query param
request.queryParamsValues("FOO")  // all values of FOO query param
request.raw();                    // raw request handed in by Jetty
request.requestMethod();          // The HTTP method (GET, ..etc)
request.scheme();                 // "http"
request.servletPath();            // the servlet path, e.g. /result.jsp
request.session();                // session management
request.splat();                  // splat (*) parameters
request.uri();                    // the uri, e.g. "http://example.com/foo"
request.url();                    // the url. e.g. "http://example.com/foo"
request.userAgent();              // user agent

response.body();               // get response content
response.body("Hello");        // sets content to Hello
response.header("FOO", "bar"); // sets header FOO with value bar
response.raw();                // raw response handed in by Jetty
response.redirect("/example"); // browser redirect to /example
response.status();             // get the response status
response.status(401);          // set status code to 401
response.type();               // get the content type
response.type("text/xml");     // set content type to text/xml
```



### 控制线程池

```java
int maxThreads = 8;
int minThreads = 2;
int timeOutMillis = 30000;
threadPool(maxThreads, minThreads, timeOutMillis);
```

### HTTPS支持

```java
secure(keystoreFilePath, keystorePassword, truststoreFilePath, truststorePassword);
```


### 自定义错误页面

```java
// Using Route
notFound((req, res) -> {
    res.type("application/json");
    return "{\"message\":\"Custom 404\"}";
});
// Using Route
internalServerError((req, res) -> {
    res.type("application/json");
    return "{\"message\":\"Custom 500 handling\"}";
});
```

### 自定义异常处理

```java
get("/throwexception", (request, response) -> {
    throw new YourCustomException();
});

exception(YourCustomException.class, (exception, request, response) -> {
    // Handle the exception here
});
```

### 结合JSON转换器

```java
import com.google.gson.Gson;

public class JsonTransformer implements ResponseTransformer {

    private Gson gson = new Gson();
     
    @Override
    public String render(Object model) {
        return gson.toJson(model);
    }

}

get("/hello", "application/json", (request, response) -> {
    return new MyMessage("Hello World");
}, new JsonTransformer());
```

简洁的转换方法

```
Gson gson = new Gson();
get("/hello", (request, response) -> new MyMessage("Hello World"), gson::toJson);
```

### 模版渲染引擎

将编写的视图文件和数据融合在一起

```java
get("template-example", (req, res) -> {
    Map<String, Object> model = new HashMap<>();
    return render(model, "path-to-template");
});

// declare this in a util-class
public static String render(Map<String, Object> model, String templatePath) {
    return new VelocityTemplateEngine().render(new ModelAndView(model, templatePath));
}
```

有很多种模版渲染引擎可供选择，Spark已经提供了扩展的library将这些引擎集成进来。只需要倒入相关maven依赖即可。比如集成FreeMarker

```xml
<dependency>
    <groupId>com.sparkjava</groupId>
    <artifactId>spark-template-freemarker</artifactId>
    <version>2.7.1</version>
</dependency>
```

### WebSocket支持

可以用来开发你的交互式微信小程序了

```java
import org.eclipse.jetty.websocket.api.*;
import org.eclipse.jetty.websocket.api.annotations.*;
import java.io.*;
import java.util.*;
import java.util.concurrent.*;

@WebSocket
public class EchoWebSocket {

    // Store sessions if you want to, for example, broadcast a message to all users
    private static final Queue<Session> sessions = new ConcurrentLinkedQueue<>();
     
    @OnWebSocketConnect
    public void connected(Session session) {
        sessions.add(session);
    }
     
    @OnWebSocketClose
    public void closed(Session session, int statusCode, String reason) {
        sessions.remove(session);
    }
     
    @OnWebSocketMessage
    public void message(Session session, String message) throws IOException {
        System.out.println("Got: " + message);   // Print message
        session.getRemote().sendString(message); // and send it back
    }

}

webSocket("/echo", EchoWebSocket.class);
init(); // Needed if you don't define any HTTP routes after your WebSocket routesCopy
```


