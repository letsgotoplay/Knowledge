# Zuul Gateway

## What is a Gateway?
Centralized place for routing,proxy,and security.
Ez to make downstream stateless to be more horizontally scalable.

## Compare Gateway Performance

[Compare Nginx/Zuul/Spring-Cloud-Gateway][1]

It is worth to notice here that The Spring Cloud Gateway has some issues about performance, and Zuul 1.X performs best under 8 core 16RAM server environment, even spring cloud gateway provides more configurable and easy ways to controll gateway's logic. Because Zuul1.X is kind of battle-tested, currently Spring Netflix Zuul is a little more favorable in tech perferences.
## Spring Netflix Zuul
Now the latest version of Zuul is 2.X. But Spring would not integrate 2.X Zuul in Spring because of Spring Cloud Gateway product.Two versions are not compatible, Zuul 1.x is Blocking and Zuul 2.X is designed to support Async Non-blocking requests. and This article will introduce Zuul 1.X mostly
### How it works
[How Zuul Works][2] (will try Zuul 2.X in future and netty)

[Spring NetFlix Zuul][4]

![zuul architect](https://camo.githubusercontent.com/4eb7754152028cdebd5c09d1c6f5acc7683f0094/687474703a2f2f6e6574666c69782e6769746875622e696f2f7a75756c2f696d616765732f7a75756c2d726571756573742d6c6966656379636c652e706e67)

Netflix uses Zuul for the following:

+ Authentication
+ Insights
+ Stress Testing
+ Canary Testing
+ Dynamic Routing
+ Service Migration
+ Load Shedding
+ Security
+ Static Response handling
+ Active/Active traffic management

Filter Types-> pre/route/post 

There some default filters added in Zuul, once you enabled the annotation @EnableZuulProxy.

Don't forget you can also register servlet filters. These filters will act before zuul filters. the **Order** works as follows:
1. servlet filters by order. it enters into spring filter, then it goes into ...
2. spring filter chains, then it goes into...
3. servlet filters left, then it goes into...
4. spring dispatch servlet, then it goes into...
5. zuul filters


### Configuration Overview 
[Zuul Config][3]
+ zuul.host.maxTotalConnections (200 default)
+ zuul.host.maxPerRouteConnections (20 default)
+ more memory more cores, the better
+ Choice of Apache HTTP Client/OkHttpClient
+ Default sensitiveHeaders: Cookie,Set-Cookie,Authorization that will be filter out when forwarding to downstreams. you need to set sensitiveHeaders to empty to skip this default action.
+ It can forward request by service id, url and forward to local Zuul itself 
+ /zuul path for extra large file stream
+ To pass information between filters, Zuul uses a RequestContext. Its data is held in a ThreadLocal specific to each request.
+ You can also disable default filters in config or provide fallback/timeout when routing.
+ Config ribbon and hystrix to provide detailed behavior for fallback/timeout and config retry policy

### Routing Example
```
ignoredPatterns: /**/admin/** #ignore route if url contains /admin/ part
users:
      path: /myusers/**  #forward route for user services start with /myusers/
      serviceId: users
local:
      path: /local/**
      url: forward:/local  # forward to zuul itself's url /local
legacy:
      sensitiveHeaders:  #set empty to pass security headers to downstream
      path: /**  #others requests are forwarded to legacy service
      url: https://example.com
```



[1]:https://engineering.opsgenie.com/comparing-api-gateway-performances-nginx-vs-zuul-vs-spring-cloud-gateway-vs-linered-b2cc59c65369
[2]:https://github.com/Netflix/zuul/wiki
[3]:https://blog.csdn.net/s573626822/article/details/83180567 
[4]:http://cloud.spring.io/spring-cloud-static/Finchley.SR2/single/spring-cloud.html#_router_and_filter_zuul





