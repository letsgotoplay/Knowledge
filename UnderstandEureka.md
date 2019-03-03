# 了解eureka

## reading pre-requistes
第一步当然是读操作手册reference了，搭建一个基本环境。
- [spring cloud netflix reference 2.10](http://cloud.spring.io/spring-cloud-static/Greenwich.RELEASE/single/spring-cloud.html#_spring_cloud_netflix)
- [netflix eureka wiki](https://github.com/Netflix/eureka/wiki)
- [nice tool comparison between eureka vs zookeeper vs consul](https://stackshare.io/stackups/consul-vs-eureka-vs-zookeeper)

## 基本搭建

通过[spring initialier](https://start.spring.io/)初始化，eureka server是：spring-cloud-netflix Eureka Server， eureka client是：Service discovery using spring-cloud-netflix and Eureka。
这是两个基本组件，引用Netflix：
> Eureka is a REST (Representational State Transfer) based service that is primarily used in the AWS cloud for locating services for the purpose of load balancing and failover of middle-tier servers. We call this service, the Eureka Server. Eureka also comes with a Java-based client component, the Eureka Client, which makes interactions with the service much easier. The client also has a built-in load balancer that does basic round-robin load balancing.

还有一概念一定要搞清楚的是eureka instance。 只要是注册到eureka server的application都叫做eureka instance. 包括eureka server自己会主动注册自己。

使用注册表的人叫client。
维护注册表的人叫server
去注册到注册表的人叫instance

Spring 2.0 有一个小改进就是现在client 不需要写手动写@enableiscoveryclient 注解了。一切全部在自动配置当中。也就是说只要有依赖，统统自动帮你搞定了。

## 了解配置属性
**三个class:**
- For Eureka server config: org.springframework.cloud.netflix.eureka.server.EurekaServerConfigBean that implements com.netflix.eureka.EurekaServerConfig. All properties are prefixed by eureka.server.
- For Eureka client config: org.springframework.cloud.netflix.eureka.EurekaClientConfigBean that implements com.netflix.discovery.EurekaClientConfig. All properties are prefixed by eureka.client.
- For Eureka instance config: org.springframework.cloud.netflix.eureka.EurekaInstanceConfigBean that implements (indirectly) com.netflix.appinfo.EurekaInstanceConfig. All properties are prefixed by eureka.instance.

每一个eureka instance都有一个instanceId。Spring 默认是使用spring.application.name.
通过registerWithEureka：true这个属性设置，instance 就会在 eureka.instance.leaseRenewalIntervalInSeconds 设置的时间间隔去向server发送心跳。默认还是30秒。如果服务器在eureka.instance.leaseExpirationDurationInSeconds（default 90s）的时间内没有收到心跳，就说这个服务已经挂了,可以从注册表移除了。并且其他的服务也就无法找到那个instance。发送心跳是一个异步的任务。并且和更新instance不是一回事儿，他两是2个不同的请求。


读了前面的eureka架构也可以了解到，从client到server每一层都维护了一个cache。 client 先从local cache里面找需要的信息，而不是直接发请求去问server要。client和server之间的互动是例行的，比如默认的30秒。然后client server各自更新自己的local cache。然后当client需要去更新注册表信息时候，他的第一个问题是： server你的信息有没有变？ 如果得到的回答是没有。则问题结束。如果不是则会获取完整的信息表并更新local cache.这样就可以节省大量的bandwidth。信息的fetch间隔和outdated管理和发送心跳差不多。都是异步task。

## 过程序列

client第一次注册心跳发送：30秒间隔
server 注册表response cache 更新： 30秒间隔
client 注册表 cache 更新： 30秒间隔
ribbon cache 更新： 30秒间隔
所以真有可能在eureka启动两分钟后才能正常使用。

## 自我保护，高可用

对于eureka的cluster就有一个属性叫：selfPreservation。这个属性需要着重了解一下。算法具体如下：
假如有two clients: 30s a heartbeat. then it's 4 heartbeats in total and server itself has one default value so the max number is 5。然后它在乘以一个系数就是eureka的自我保护的threshold。默认值是0.85， 然后roundtoint(5*0.85)=5。 所以在eureka的面板上如果server看到heartbeat少于这个threhold就会自动进入selfPreservation模式。
这个模式的意思就是server不会在剔除注册表里的instance了。因为这可能是这一阵子的网络差的原因没有收到足够的心跳。如果下一分钟心跳都收到了，而server在把这个注册进去会花费大量的时间和造成server不可用（因为注册表里啥都没有，前面也说到注册一个服务有可能需要长达两分钟）。

在低环境可以直接把这个模式关了。也可以把那个系数的值调低点。这样就能保证注册表的正常更新而不至于进入自我保护模式。

在client default zone里面可以配置多个server地址。但是client会优先去第一个server地址注册，当第一个server不可用的时候才会去找第二个server 地址。 所以server 和server之间有一个自动sync注册表信息机制，也就是server会自动复制它接收到的请求到其他的兄弟节点。

## Spring actuator
举个例子，在一个cluster环境，我的这个服务需要维护了，可以不可以通知server告诉他我现在正在维护呢？
答案：可以,通过actuator的一些endpoint,我设置自己的eureka client instance info去通知eureka server：
```bash
curl -X "POST" "http://localhost:8895/test/actuator/service-registry?status=DOWN" \
   -H "Content-Type: application/vnd.spring-boot.actuator.v2+json;charset=UTF-8"
```

## 小结

简单但是没有consul好使。









