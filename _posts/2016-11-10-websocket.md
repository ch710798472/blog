---
layout: post
title: websocket初探
category: java
modified: 2016-11-10
tags: [websocket,spring boot,java]
comments: true
pinned: true
excerpt: "1. websocket介绍
　　2.websocket使用...."
---
### 1. 介绍  
### 1.1 websocket  
  WebSocket protocol 是HTML5一种新的协议。它实现了浏览器与服务器全双工通信(full-duplex)。一开始的握手需要借助HTTP请求完成。[百科](http://baike.baidu.com/link?url=tAaaueb4FPkhCtWLb8BUfQ8qcAQ4gzrNUu7gK4JXM8eBrxa4UobfvYhQcWW0nqrGD0drV_SVcmCiPfamVznX-0_ZMhSRuS51GA6qjKArez3)
  简单来说，就是websocket实现了全双工通信，没有采用轮询和长连接来实现，而是在建立连接过程中通过http报头中connection：upgrade字段进行协议升级，从而建立一条全双工通道。  
  WebSocket技术的优点有：  
    1）通过第一次HTTP Request建立了连接之后，后续的数据交换都不用再重新发送HTTP Request，节省了带宽资源；   
    2) WebSocket的连接是双向通信的连接，在同一个TCP连接上，既可以发送，也可以接收;   
    3)具有多路复用的功能(multiplexing)，也即几个不同的URI可以复用同一个WebSocket连接。这些特点非常类似TCP连接，但是因为它借用了HTTP协议的一些概念，所以被称为了WebSocket
### 1.2 spring boot  
  Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。通过这种方式，Boot致力于在蓬勃发展的快速应用开发领域（rapid application development）成为领导者。
  其实spring boot解决了最主要的问题就是现在的spring项目中有大量的xml文件的配置以及复杂的依赖管理，在spring boot几乎都可以使用注解来进行配置，大量减少了xml文件的使用，甚至web项目都不需要web.xml文件了，对于各种依赖spring boot也提供了几个核心的基础pom文件，能满足大部分人的日常需求了（整个pom文件看起来清爽多了）。
      名称	说明
      spring-boot-starter	核心 POM，包含自动配置支持、日志库和对 YAML 配置文件的支持。
      spring-boot-starter-amqp	通过 spring-rabbit 支持 AMQP。
      spring-boot-starter-aop	包含 spring-aop 和 AspectJ 来支持面向切面编程（AOP）。
      spring-boot-starter-batch	支持 Spring Batch，包含 HSQLDB。
      spring-boot-starter-data-jpa	包含 spring-data-jpa、spring-orm 和 Hibernate 来支持 JPA。
      spring-boot-starter-data-mongodb	包含 spring-data-mongodb 来支持 MongoDB。
      spring-boot-starter-data-rest	通过 spring-data-rest-webmvc 支持以 REST 方式暴露 Spring Data 仓库。
      spring-boot-starter-jdbc	支持使用 JDBC 访问数据库。
      spring-boot-starter-security	包含 spring-security。
      spring-boot-starter-test	包含常用的测试所需的依赖，如 JUnit、Hamcrest、Mockito 和 spring-test 等。
      spring-boot-starter-velocity	支持使用 Velocity 作为模板引擎。
      spring-boot-starter-web	支持 Web 应用开发，包含 Tomcat 和 spring-mvc。
      spring-boot-starter-websocket	支持使用 Tomcat 开发 WebSocket 应用。
      spring-boot-starter-ws	支持 Spring Web Services。
      spring-boot-starter-actuator	添加适用于生产环境的功能，如性能指标和监测等功能。
      spring-boot-starter-remote-shell	添加远程 SSH 支持。
      spring-boot-starter-jetty	使用 Jetty 而不是默认的 Tomcat 作为应用服务器。
      spring-boot-starter-log4j	添加 Log4j 的支持。
      spring-boot-starter-logging	使用 Spring Boot 默认的日志框架 Logback。
      spring-boot-starter-tomcat	使用 Spring Boot 默认的 Tomcat 作为应用服务器。
  但是大量使用注解的同时也带来了一个麻烦，理解注解背后的实现原理较为困难，注解也并不是万能解决方案它也是有弊端的。
### 2. 使用  
  首先新建一个spring boot项目，一个普通的maven java项目即可，pom文件使用最基本的spring boot配置，在添加如下依赖
  
        <!--velocity模板依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-velocity</artifactId>
        </dependency>
         <!--websocket依赖-->
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-websocket</artifactId>
         </dependency>
         <!--fastjson依赖-->
         <dependency>
             <groupId>com.alibaba</groupId>
             <artifactId>fastjson</artifactId>
             <version>1.2.20</version>
         </dependency>
         
  一个是velocity模板的依赖用来写前端页面模板，一个是websocket依赖，一个是fastjson依赖用来支持websocket在传输过程中encode和decode。  
#### 2.1 webSocket代码  
  Java后端websocket需要两个类，一个是WebSocketConfig另一个就是WebSocketController。第一个是提供websocket使用的bean，第二个是消息处理类。 
  `WebSocketConfig.java`:  
  
```
@Configuration
  public class WebSocketConfig {
      @Bean
      public ServerEndpointExporter serverEndpointExporter (){
          return new ServerEndpointExporter();
      }
  }
```  

  `WebSocketController.java`:  
  
```  
@ServerEndpoint(value = "/websocket", encoders = {ServerEncoder.class}, decoders = {ServerDecoder.class})
@Component
@RestController
@RequestMapping("/websocket")
public class WebSocketController {

    private static int onlineCount = 0;

    private static CopyOnWriteArraySet<WebSocketController> webSocketSet = new CopyOnWriteArraySet<WebSocketController>();

    private Session session;

    @RequestMapping("/chat")
    public ModelAndView websocket() {
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("count_num", getOnlineCount());
        ModelAndView modelAndView = new ModelAndView("websocket");
        modelAndView.addAllObjects(map);
        return modelAndView;
    }

    @OnOpen
    public void onOpen(Session session) throws IOException, EncodeException {
        this.session = session;
        webSocketSet.add(this);
        addOnlineCount();
        System.out.println("有新链接加入!当前在线人数为" + getOnlineCount() + ", 你的ID是：" + session.getId());
        //推送当前在线人数
        Message message = new Message("open", getOnlineCount() + "", Integer.valueOf(session.getId()));
        for (WebSocketController item : webSocketSet) {
            item.sendObjectMessage(message);
        }
        Message userId = new Message("user_id", getOnlineCount() + "", Integer.valueOf(session.getId()));
        this.sendObjectMessage(userId);
    }

    @OnClose
    public void onClose() throws IOException, EncodeException {
        webSocketSet.remove(this);
        subOnlineCount();
        System.out.println("有一链接关闭,ID=" + session.getId() + "!当前在线人数为" + getOnlineCount());
        //推送当前在线人数
        Message message = new Message("close", getOnlineCount() + "", Integer.valueOf(session.getId()));
        for (WebSocketController item : webSocketSet) {
            item.sendObjectMessage(message);
        }
    }

    @OnMessage
    public void onMessage(Message message, Session session) throws IOException, EncodeException {
        System.out.println("来自客户端ID=" + session.getId() + "的消息:" + message.toString());
        // 群发消息
        message.setUserId(Integer.valueOf(session.getId()));
        for (WebSocketController item : webSocketSet) {
//            item.sendMessage(message);
            item.sendObjectMessage(message);
        }
    }

    public void sendMessage(String message) throws IOException {
        this.session.getBasicRemote().sendText(message);
    }

    public void sendObjectMessage(Object object) throws IOException, EncodeException {
        this.session.getBasicRemote().sendObject(object);
    }

    public static synchronized int getOnlineCount() {
        return WebSocketController.onlineCount;
    }

    public static synchronized void addOnlineCount() {
        WebSocketController.onlineCount++;
    }

    public static synchronized void subOnlineCount() {
        WebSocketController.onlineCount--;
    }

}
```  

  我们使用了四个注解  
  `@ServerEndpoint(value = "/websocket", encoders = {ServerEncoder.class}, decoders = {ServerDecoder.class})`定义websocke拦截的请求url，以及数据传输过程自定义解码编码使用的方式  
  `@Component`  
  `@RestController`  
  `@RequestMapping("/websocket")`定义spring boot拦截url请求，配合websocket()函数上的注解，最终请求的地址是[http://localhost:8080/websocket/hi](http://localhost:8080/websocket/hi)  
  也就是说输入上述网址，就会得到一个页面，在这个页面上可以使用websocket。接下来我们看一下websocket()函数返回的vm页面：
  `websocket.vm`页面写在/resources/templates里面  
  
```  
<!DOCTYPE HTML>
<html>
<head>
    <base href="localhost://localhost:8080/">
    <title>聊天室</title>
</head>

<body>
<br/>
<div id="user_id"></div>
<div id="count_num">当前在线人数: ${count_num}</br></div>
<input id="text" type="text"/>
<button onclick="send()">Send</button>
<button onclick="closeWebSocket()">Close</button>
<div id="message">
</div>
</body>

<script type="text/javascript">
    var websocket = null;
    var user_id = null;
    //判断当前浏览器是否支持WebSocket
    if ('WebSocket' in window) {
        websocket = new WebSocket("ws://localhost:8080/websocket");
    }
    else {
        alert('Not support websocket')
    }

    //连接发生错误的回调方法
    websocket.onerror = function () {
        setMessageInnerHTML("error");
    };

    //连接成功建立的回调方法
    websocket.onopen = function (event) {
        setMessageInnerHTML("系统消息--> 链接服务器成功！");
    }

    //接收到消息的回调方法
    websocket.onmessage = function (event) {
        var json = JSON.parse(event.data);
        if(json.type == "onLineCount"){
            countOnlineNumber(json.message);
        }
        if(json.type == "msg"){
            setMessageInnerHTML("接收到 "+ json.userId + " 发来的消息： " + json.message);
        }
        if(json.type == "close"){
            countOnlineNumber(json.message);
            setMessageInnerHTML("系统消息--> "+ json.userId + " 悄悄的离开了! ");
        }
        if(json.type == "open"){
            countOnlineNumber(json.message);
            //showId(json.userId);
            setMessageInnerHTML("系统消息--> "+ json.userId + " 悄悄的来了! ");
        }
        if(json.type == "user_id"){
            showId(json.userId);
        }
    }

    //连接关闭的回调方法
    websocket.onclose = function () {
        setMessageInnerHTML("系统消息--> 断开与服务器的链接！");
    }

    //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
    window.onbeforeunload = function () {
        websocket.close();
    }

    //将消息显示在网页上
    function setMessageInnerHTML(innerHTML) {
        document.getElementById('message').innerHTML += innerHTML + '<br/>';
    }

    //显示在线人数
    function countOnlineNumber(innerHTML) {
        document.getElementById('count_num').innerHTML = "当前在线人数:"+innerHTML + '<br/>';
    }

    //显示个人ID
    function showId(innerHTML) {
        document.getElementById('user_id').innerHTML = "你的id：" + innerHTML + '<br/>';
    }

    //关闭连接
    function closeWebSocket() {
        websocket.close();
    }

    //发送消息
    function send() {
        var message = document.getElementById('text').value;
        var json = {
            'type' : "msg",
            'message' : message
        };
        websocket.send(JSON.stringify(json));
        //websocket.send(message);
    }
</script>
</html>
```  

#### 2.2 运行  
  spring boot可以直接执行main函数运行，速度快而且方便调试，如果需要打包成war在部署的话耗时耗力，在前期验证程序功能可以很方便。
  新建文件Application.java：  

```
  @Configuration
  @ComponentScan
  @EnableAutoConfiguration
  public class Application {
      public static void main(String[] args) {
          SpringApplication application = new SpringApplication(Application.class);
          application.run();
      }
  }
```  
  
  启动main函数可以看到内嵌的tomcat启动过程中的日志信息，输入网址，期望的聊天界面就出来了，效果如下：  
  ![1](https://github.com/ch710798472/blog/raw/gh-pages/img/websocket2.png)