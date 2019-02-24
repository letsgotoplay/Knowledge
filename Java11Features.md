# 为什么要聊聊java

## 版本
现在众多产品依然使用java8于production环境，但是2019年一月oracle已经**终止**了商业用户long-term support（简称lts），下一步是于2020年12月终止个人用户的lts。这意味着Java新版本升级将要成为必然。

值得一说的是现在Java每6个月就会发布一个版本，并且每三年会有一个long-term-support。 这也就是为什么Java现在版本升级这么快的原因。比如今年是Java11的lts的启动年，那么到2021年的Java17会是下一个lts版本。 所以了解Java11有哪些新功能对我们的coding和将来的Java升级是大有好处的。

## Java11 Features

- Nest-Based Access Control
  - JVM update
- Dynamic Class File Constants
  - Compiler update
- Improve Aarch64 Intrinsics
  - Improving the existing string and array intrinsics for AArch64 cpu processors 
- Remove the Java EE and CORBA Modules
  - mini size from SE version
- Key Agreement with Curve25519 and Curve448
  - Implementation of the cryptographic graphics schemes
- Unicode 10
  - Updated the existing platform APIs to version 10.0 
- Deprecate the Rhino JavaScript Engine
- Deprecate the Pack200 Tools and API
- Transport Layer Security (TLS) 1.3
   - TLS1.3 standard implementation
- Low overhead heap profiling
  - JVMTI tool spport upgrade
- ChaCha20 and Poly1305 Cryptographic Algorithms
  - Implementation of a stream encryption 

Interesting Features:

- Epsilon: A No-Op Garbage Collector
  - mainly for researcher,performance tester to set JVM without any GC.
  - Also interestly how we can do a [application without need of GC like Log4J](https://www.infoq.com/news/2016/05/log4j-garbage-free)
- ZGC: A Scalable Low-Latency Garbage Collector 
(Experimental)
- G1: pararell GC
- Flight Recorder
  - [record-java-application-events](https://openjdk.java.net/jeps/328)
- HTTP Client (default)
  - 新的http-client 跟像其他语言的api了
  ```java
  // two ways of new http client, sync way as default, or use async API
  var request = HttpRequest.newBuilder()
    .uri(URI.create("https://www.example.com"))
    .GET()
    .build();
  var client = HttpClient.newHttpClient();
  HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
  System.out.println(response.body());

  // or use Async way
  var request = HttpRequest.newBuilder()
    .uri(URI.create("https://winterbe.com"))
    .build(); // We can omit the .GET() call as it's the default request method.
  var client = HttpClient.newHttpClient();
  client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
      .thenApply(HttpResponse::body)
      .thenAccept(System.out::println);

  // and other calls like post() in request and build a basic auth while build http client. go check API
  ```
- Local Variable Syntax for Lambda Parameters
  - 用var来定义本地变量 和lambda一起 省不少时间
  ```java
  //before Java 10
  String text = "Hello Java 9";
  //Java 11
  var text = "Hello Java 11";
  var text = "Hello Java 11";
  text = 23;  // Incompatible types
  final var text = "Banana";
  text = "Joe";   // Cannot assign a value to final variable 'text'

  // Cannot infer type:
  var a; // Cannot infer type
  var nothing = null; // Cannot infer type
  var lambda = () -> System.out.println("Pity!"); // Cannot infer type
  var method = this::someMethod; // Cannot infer type

  // eaier
  var myList = new ArrayList<Map<String, List<Integer>>>();

  for (var current : myList) {
      // current is infered to type: Map<String, List<Integer>>
      System.out.println(current);
  }
    // Inference of lambda parameters
	Consumer<String> printer = (var s) -> System.out.println(s); 
	// but no mix of "var" and declared types possible
	// BiConsumer<String, String> printer = (var s1, String s2) -> System.out.println(s1 + " " + s2);
	// Useful for Type Annotations
	BiConsumer<String, String> printer = (@Nonnull var s1, @Nullable var s2) -> System.out.println(s1 + (s2 == null ? "" : " " + s2));
  ```

- Launch Single-File Source Code Programs
  - Java 文件可以直接启动 或者用类似shell 脚本启动
  ```bash
  # java HelloWorld.java
  // instead of
  # javac HelloWorld.java
  # java -cp . hello.World
  // or declare 
  #!/path/to/java --source version
  # ./HelloWorld.java
  ```
- new APIs
 ```java
    // Immutable List Creation
	var list = List.of("A", "B", "C");
	var copy = List.copyOf(list);
	System.out.println(list == copy);   // true

	//map
	var map = Map.of("A", 1, "B", 2);
    System.out.println(map);    // {B=2, A=1}
    
    // Stream
    Stream.ofNullable(null).count();
    Stream.of(1, 2, 3, 2, 1)
    .dropWhile(n -> n < 3)
    .collect(Collectors.toList());  // [3, 2, 1]

    Stream.of(1, 2, 3, 2, 1)
    .takeWhile(n -> n < 3)
    .collect(Collectors.toList());  // [1, 2]
    //optinal of && ofNullable
    Optional.of("foo").orElseThrow();     // foo
    Optional.of("foo").stream().count();  // 1
    Optional.ofNullable(null)
    .or(() -> Optional.of("fallback"))
    .get();                           // fallback

   //String 
   " ".isBlank();                // true
   " Foo Bar ".strip();          // "Foo Bar"
   " Foo Bar ".stripTrailing();  // " Foo Bar"
   " Foo Bar ".stripLeading();   // "Foo Bar "
   "Java".repeat(3);             // "JavaJavaJava"
   "A\nB\nC".lines().count();    // 3

   //InputStreams tranferTo()
   var classLoader = ClassLoader.getSystemClassLoader();
   var inputStream = classLoader.getResourceAsStream("myFile.txt");
   var tempFile = File.createTempFile("myFileCopy", "txt");
   try (var outputStream = new FileOutputStream(tempFile)) {
       inputStream.transferTo(outputStream);
   }

 ``` 

## Future
现在已经有了jdk13的beta，主要的新功能如下：
- switch style change
- raw string literal

## Notice
注意到了现在的api设计是越来越直接明了，特别是在将要到来**异步**（好题材）的时代，当我们看到一个方法名的时候，我们就已经知道这个方法到底是在干嘛，并且当有多个方法串联的时候，展示在我们面前的一长串都是非常的口语化和容易理解。说到api设计，就想到一个好的api设计的必读资料：[API-design-principles](https://wiki.qt.io/API_Design_Principles)

为什么要一个好的api设计？
- 人：team 协同工作， 看着舒服， 提高效率，而不是去一直查这个参数是什么意思。
举个例子 a.setsomething（true）。不查看具体方法，天知道这是在干什么。效率低下。
- 环境： 云容器微服务时代，各个容器，微服务，application之间互相调用。

## Reference:
- [java-se-support-roadmap](https://www.oracle.com/technetwork/java/java-se-support-roadmap.html)
- [java-12-intersting-features](https://dzone.com/articles/interesting-jdk-12-features-to-watch-out-for)
- [java-11-new-features](https://codingcompiler.com/17-new-features-in-java-11-jdk-11-features/)
- [java-no-gc](https://www.infoq.com/news/2017/03/java-epsilon-gc)
- [java-11-tutorial](https://winterbe.com/posts/2018/09/24/java-11-tutorial/)
