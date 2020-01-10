# 参考：

[外行人都能看懂的SpringCloud，错过了血亏！](https://segmentfault.com/a/1190000016157665)

[Spring Cloud 从入门到精通](http://blog.didispace.com/spring-cloud-learning/?utm_term=yudaoyuanma)

[Spring Cloud ](https://windmt.com/tags/Spring-Cloud/)

[Spring Cloud ](http://cmsblogs.com/?cat=166&paged=29)

[SpringCloud系列教程](https://www.geekdigging.com/category/springcloud/)

[SpringCloud 之 Eureka 服务注册中心 [ 注册数据、视图微服务 ]（一）](https://blog.csdn.net/Hack_Different/article/details/103049169)

[TOC]

# 一、Spring Cloud介绍

## 1、什么是微服务

要了解 Spring Cloud 就绕不开微服务这个概念。因为 Spring Cloud 是 Spring 为微服务架构思想做的一个一站式实现。从某种程度是可以简单的理解为，微服务是一个概念、一个项目开发的架构思想。Spring Cloud 是微服务架构的一种 Java 实现。

微服务的概念源于 Martin Fowler 于 2014 年 3 月 25 日写的一篇文章 [Microservices](https://martinfowler.com/articles/microservices.html)（中文版翻译[点击查看](http://www.cnblogs.com/liuning8023/p/4493156.html)）

微服务架构模式（Microservices Architecture Pattern）的目的是**将大型的、复杂的、长期运行的应用程序构建为一组相互配合的服务，每个服务都可以很容易得局部改良**。 Micro 这个词意味着每个服务都应该足够小，但是，这里的小不能用代码量来比较，而应该是从业务逻辑上比较 —— 符合 [SRP 原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)的才叫微服务。

关于微服务的一个形象表达：
[![img](.\img\006tNc79ly1fqc291i8v8j30go0aygly-1577886504546.jpg)](https://src.windmt.com/img/006tNc79ly1fqc291i8v8j30go0aygly.jpg)
X 轴：水平扩展，即在负载均衡服务器后增加多个运行实例
Z 轴：数据库的扩展，即分库分表
Y 轴：功能分解，即将不同职能的模块分成不同的服务

## 2、微服务架构优势

**复杂度可控：**在将应用分解的同时，规避了原本复杂度无止境的积累。每一个微服务专注于单一功能，并通过定义良好的接口清晰表述服务边界。由于体积小、复杂度低，每个微服务可由一个小规模开发团队完全掌控，易于保持高可维护性和开发效率。
**独立部署：**由于微服务具备独立的运行进程，所以每个微服务也可以独立部署。当某个微服务发生变更时无需编译、部署整个应用。由微服务组成的应用相当于具备一系列可并行的发布流程，使得发布更加高效，同时降低对生产环境所造成的风险，最终缩短应用交付周期。
**技术选型灵活：**微服务架构下，技术选型是去中心化的。每个团队可以根据自身服务的需求和行业发展的现状，自由选择最适合的技术栈。由于每个微服务相对简单，故需要对技术栈进行升级时所面临的风险就较低，甚至完全重构一个微服务也是可行的。
**容错（fault isolation）：**当某一组建发生故障时，在单一进程的传统架构下，故障很有可能在进程内扩散，形成应用全局性的不可用。在微服务架构下，故障会被隔离在单个服务中。若设计良好，其他服务可通过重试、平稳退化等机制实现应用层面的容错。
**扩展：**每个服务**可以各自进行 x 扩展和 z 扩展，而且，每个服务可以根据自己的需要部署到合适的硬件服务器上**。当应用的不同组件在扩展需求上存在差异时，微服务架构便体现出其灵活性，因为每个服务可以根据实际需求独立进行扩展。

## 3、为什么微服务架构需要 Spring Cloud

简单来说，**服务化的核心就是将传统的一站式应用根据业务拆分成一个一个的服务，而微服务在这个基础上要更彻底地去耦合（不再共享 DB、KV，去掉重量级 ESB），并且强调 DevOps 和快速演化**。这就要求我们必须采用与一站式时代、泛 SOA 时代不同的技术栈，而 `Spring Cloud` 就是其中的佼佼者。

> `DevOps` 是英文 `Development` 和 `Operations` 的合体，他要求开发、测试、运维进行一体化的合作，进行更小、更频繁、更自动化的应用发布，以及围绕应用架构来构建基础设施的架构。这就要求应用充分的内聚，也方便运维和管理。这个理念与微服务理念不谋而合。

### 3.1、从使用 nginx 说起

最初的服务化解决方案是给提供相同服务提供一个统一的域名，然后服务调用者向这个域名发送 HTTP 请求，由 `Nginx` 负责请求的分发和跳转。

![img](.\img\006tNc79ly1fqc316du2nj30eq0dgdfx.jpg)

这种架构存在很多问题:

- `Nginx` 作为中间层，**在配置文件中耦合了服务调用的逻辑，这削弱了微服务的完整性，也使得 `Nginx` 在一定程度上变成了一个重量级的 `ESB`(中心化)。**
- 服务的信息分散在各个系统，无法统一管理和维护。**每一次的服务调用都是一次尝试，服务消费者并不知道有哪些实例在给他们提供服务**。这不符合 `DevOps` 的理念。
- 无法直观的看到服务提供者和服务消费者当前的运行状况和通信频率。这也不符合 `DevOps` 的理念。
- 消费者的失败重发，负载均衡等都没有统一策略，这加大了开发每个服务的难度，不利于快速演化。

为了解决上面的问题，我们**需要一个现成的中心组件对服务进行整合，将每个服务的信息汇总，包括服务的组件名称、地址、数量等。服务的调用方在请求某项服务时首先通过中心组件获取提供这项服务的实例的信息（IP、端口等），再通过默认或自定义的策略选择该服务的某一提供者直接进行访问**。所以，我们引入了 `Dubbo`。

### 3.2、基于 Dubbo 实现微服务

Dubbo 是阿里开源的一个 `SOA` 服务治理解决方案，文档丰富，在国内的使用度非常高。

[![img](.\img\006tNc79ly1fqc31r2nulj30e90bsdfv.jpg)](https://src.windmt.com/img/006tNc79ly1fqc31r2nulj30e90bsdfv.jpg)

使用 `Dubbo` 构建的微服务，已经可以比较好地解决上面提到的问题：

- 调用中间层变成了可选组件，消费者可以直接访问服务提供者。
- 服务信息被集中到 `Registry` 中，形成了服务治理的中心组件。
- 通过 `Monitor` 监控系统，可以直观地展示服务调用的统计信息。
- `Consumer` 可以进行负载均衡、服务降级的选择。

但是对于微服务架构而言，`Dubbo` 也并不是十全十美的：

- `Registry` 严重依赖第三方组件（`zookeeper` 或者 `redis`），当这些组件出现问题时，服务调用很快就会中断。
- `Dubbo` 只支持 `RPC（服务远程调用，机制是根据语言的API来定义的）` 调用。**使得服务提供方与调用方在代码上产生了强依赖，服务提供者需要不断将包含公共代码的 jar 包打包出来供消费者使用。一旦打包出现问题，就会导致服务调用出错。**

### 3.3、新的选择 ——Spring Cloud

`Spring Cloud`是**一系列框架的有序集合**。它利用`Spring Boot`的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用`Spring Boot`的开发风格做到一键启动和部署。**`Spring Cloud`并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过Spring Boot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包**。

 根据微服务架构在各方面的要素，我们把 `Spring Cloud` 与 `Dubbo` 进行一番对比。

**微服务（Microservices）：** 是一种架构风格，一**个大型复杂软件应用由一个或多个微服务组成。系统中的各个微服务可被独立部署，各个微服务之间是松耦合的**。每个微服务仅关注于完成一件任务并很好地完成该任务。在所有情况下，每个任务代表着一个小的业务能力。

**Dubbo：** `Dubbo`是一个分布式服务框架，致力于提供高性能和透明化的 `RPC` 远程服务调用方案，以及 `SOA` 服务治理方案。简单的说，**`Dubbo` 就是个服务框架，说白了就是个远程服务调用的分布式框架**。

**springcloud：** `springcloud`是一整套的微服务解决方案，提供了一系列的工具或者框架。

其实把 `Spring Cloud` 和 `Dubbo` 放一起对比有点不公平，`Dubbo` 只是实现了服务治理，而 `Spring Cloud` 下面有 23 个子项目（截止到 2018.4）分别覆盖了微服务架构下的方方面面，服务治理只是其中的一个方面，一定程度来说，**`Dubbo` 只是 `Spring Cloud Netflix` 中的一个子集**。但是在选择框架上，方案完整度恰恰是一个需要重点关注的内容。

| Dubbo        | Spring Cloud  |                              |
| :----------- | :------------ | ---------------------------- |
| 服务注册中心 | Zookeeper     | Spring Cloud Netflix Eureka  |
| 服务调用方式 | RPC           | REST API                     |
| 服务监控     | Dubbo-monitor | Spring Boot Admin            |
| 断路器       | 不完善        | Spring Cloud Netflix Hystrix |
| 服务网关     | 无            | Spring Cloud Netflix Zuul    |
| 分布式配置   | 无            | Spring Cloud Config          |
| 服务跟踪     | 无            | Spring Cloud Sleuth          |
| 消息总线     | 无            | Spring Cloud Bus             |
| 数据流       | 无            | Spring Cloud Stream          |
| 批量任务     | 无            | Spring Cloud Task            |
| ……           | ……            | ……                           |

> 服务调用方式：`Spring Cloud` 抛弃了 `Dubbo` 的 **RPC 通信**，采用的是基于 `HTTP` 的 **REST 方式**。严格来说，这两种方式各有优劣。虽然从一定程度上来说，**后者牺牲了服务调用的性能，但也避免了上面提到的原生 RPC 带来的问题（开发语言局限性，所有服务需要采用相同的语言开发）。而且 REST 相比 RPC 更为灵活，服务提供方和调用方的依赖只依靠一纸契约，不存在代码级别的强依赖**，这在强调快速演化的微服务环境下，显得更加合适。

### 3.4、Spring Cloud 技术概览

下图展示了 Spring Cloud 的完整技术组成：

[<img src=".\img\006tNc79ly1fqc4dcx75gj31ab0o775w.jpg" alt="img" style="zoom:150%;" />](https://src.windmt.com/img/006tNc79ly1fqc4dcx75gj31ab0o775w.jpg)

- 服务治理：这是 Spring Cloud 的核心。目前 Spring Cloud 主要通过整合 Netflix 的相关产品来实现这方面的功能（Spring Cloud Netflix），包括用于服务注册和发现的 `Eureka`，调用断路器 `Hystrix`，调用端负载均衡 `Ribbon`，Rest 客户端 `Feign`，智能服务路由 `Zuul`，用于监控数据收集和展示的 `Spectator`、`Servo`、`Atlas`，用于配置读取的 `Archaius` 和提供 `Controller` 层 `Reactive` 封装的 `RxJava`。除此之外，针对于服务的注册和发现，除了 `Eureka`，Spring Cloud 也整合了 `Consul` 和 `Zookeeper` 作为备选，但是因为这两个方案在 CAP 理论上都遵循 CP 而不是 AP，所以官方并没有推荐使用。

  > `Feign` 和 `RxJava` 并不是 `Netflix` 的产品，但是被整合到了 `Spring Cloud Netflix` 中。

- 分布式链路监控：`Spring Cloud Sleuth` 提供了全自动、可配置的数据埋点，以收集微服务调用链路上的性能数据，并发送给 Zipkin 进行存储、统计和展示。

- 消息组件：Spring Cloud Stream 对于分布式消息的各种需求进行了抽象，包括发布订阅、分组消费、消息分片等功能，实现了微服务之间的异步通信。Spring Cloud Stream 也集成了第三方的 RabbitMQ 和 Apache Kafka 作为消息队列的实现。而 Spring Cloud Bus 基于 Spring Cloud Stream，主要提供了服务间的事件通信（比如刷新配置）。

- 配置中心：基于 Spring Cloud Netflix 和 Spring Cloud Bus，Spring 又提供了 Spring Cloud Config，实现了配置集中管理、动态刷新的配置中心概念。配置通过 Git 或者简单文件来存储，支持加解密。

- 安全控制：Spring Cloud Security 基于 OAuth2 这个开放网络的安全标准，提供了微服务环境下的单点登录、资源授权、令牌管理等功能。

- 命令行工具：Spring Cloud Cli 提供了以命令行和脚本的方式来管理微服务及 Spring Cloud 组件的方式。

- 集群工具：Spring Cloud Cluster 提供了集群选主、分布式锁（暂未实现）、一次性令牌（暂未实现）等分布式集群需要的技术组件。

**以下排序不分先后**

- **Spring Cloud Config 配置中心：** 利用 Git 集中管理程序的配置。
- **Spring Cloud Netflix Eureka：** 服务中心（类似于管家的概念，需要什么直接从这里取，就可以了），一个基于 REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移。
- **Spring Cloud Netflix Hystrix 熔断器：** 容错管理工具，旨在通过熔断机制控制服务和第三方库的节点，从而对延迟和故障提供更强大的容错能力。
- **Spring Cloud Netflix Zuul 网关：** 是在云平台上提供动态路由，监控，弹性，安全等边缘服务的框架。Web 网站后端所有请求的前门。
- **Spring Cloud Netflix Archaius：** 配置管理 API，包含一系列配置管理API，提供动态类型化属性、线程安全配置操作、轮询框架、回调机制等功能。
- **Spring Cloud Netflix Ribbon：** 负载均衡。
- **Spring Cloud Netflix Fegin：** REST客户端。
- **Spring Cloud Bus：** 消息总线，利用分布式消息将服务和服务实例连接在一起，用于在一个集群中传播状态的变化。
- **Spring Cloud Security：** 安全控制。
- **Spring Cloud Sleuth：** 分布式链路监控，SpringCloud 应用的分布式追踪系统，和 Zipkin，HTrace，ELK 兼容。
- **Spring Cloud Stream：** 消息组件，基于 Redis，Rabbit，Kafka 实现的消息微服务，简单声明模型用以在 Spring Cloud 应用中收发消息。

#### 3.4.1、Eureka

`Eureka` 是 `Netflix` 开源的一款提供服务注册和发现的产品，它提供了完整的 `Service Registry` 和 `Service Discovery` 实现。也是 Spring Cloud 体系中最重要最核心的组件之一。

用大白话讲，`Eureka` 就是一个服务中心，**将所有的可以提供的服务都注册到它这里来管理，其它各调用者需要的时候去注册中心获取，然后再进行调用，避免了服务之间的直接调用，方便后续的水平扩展、故障转移等**。如下图：

![image-20200102105402624](.\img\image-20200102105402624.png)

当然服务中心这么重要的组件一但挂掉将会影响全部服务，因此**需要搭建 `Eureka` 集群来保持高可用性，生产中建议最少两台。随着系统的流量不断增加，需要根据情况来扩展某个服务**，`Eureka` 内部已经提供均衡负载的功能，只需要增加相应的服务端实例既可。那么在系统的运行期间某个实例挂了怎么办？Eureka 内容有一个心跳检测机制，如果某个实例在规定的时间内没有进行通讯则会自动被剔除掉，避免了某个实例挂掉而影响服务。

因此使用了 `Eureka` 就自动具有了**注册中心、负载均衡、故障转移的功能**。

#### 3.4.2、Hystrix

在微服务架构中通常会有多个服务层调用，**基础服务的故障可能会导致级联故障**，进而造成整个系统不可用的情况，这种现象被称为**服务雪崩效应**。服务雪崩效应是**一种因 “服务提供者” 的不可用导致 “服务消费者” 的不可用，并将不可用逐渐放大的过程**。

如下图所示：A 作为服务提供者，B 为 A 的服务消费者，C 和 D 是 B 的服务消费者。A 不可用引起了 B 的不可用，并将不可用像滚雪球一样放大到 C 和 D 时，雪崩效应就形成了。

![img](.\img\006tNc79ly1fqc5fkiehej30g60e30st.jpg)

在**这种情况下就需要整个服务机构具有故障隔离的功能，避免某一个服务挂掉影响全局**。在 Spring Cloud 中 `Hystrix` 组件就扮演这个角色。

> `Hystrix` 会在某个服务连续调用 N 次不响应的情况下，立即通知调用端调用失败，避免调用端持续等待而影响了整体服务。`Hystrix` 间隔时间会再次检查此服务，如果服务恢复将继续提供服务。

#### 3.4.3、Hystrix Dashboard 和 Turbine

当熔断发生的时候需要迅速的响应来解决问题，避免故障进一步扩散，那么对熔断的监控就变得非常重要。熔断的监控现在有两款工具：`Hystrix-dashboard` 和 `Turbine`

`Hystrix-dashboard` 是一款针对 `Hystrix` 进行实时监控的工具，通过 `Hystrix Dashboard` 我们可以直观地看到各 `Hystrix Command` 的请求响应时间，请求成功率等数据。但是只使用 `Hystrix Dashboard` 的话，你只能看到单个应用内的服务信息，这明显不够。我们需要一个工具能让我们汇总系统内多个服务的数据并显示到 `Hystrix Dashboard` 上，这个工具就是 `Turbine`.
监控的效果图如下：

[![img](.\img\006tNc79ly1fqc5gk1wivj30gn0am0th.jpg)](https://src.windmt.com/img/006tNc79ly1fqc5gk1wivj30gn0am0th.jpg)

#### 3.4.4、Spring Cloud Config

> 随着微服务不断的增多，每个微服务都有自己对应的配置文件。在研发过程中有测试环境、UAT环境（用户接受度测试 即验收测试，所以UAT环境主要是用来作为客户体验的环境。）、生产环境，因此每个微服务又对应至少三个不同环境的配置环境。这么多的配置环境，如果需要修改某个公共服务的配置信息，如：缓存，数据库等，难免会产生混乱，这个时候就需要引入Spring Cloud另外一个组件：Spring Cloud Config。

Spring Cloud Config 是一个解决分布式系统的配置管理方案。她包含了`Client`和`Server`两个部分，`Server`提供配置文件的存储、以接口的形式将配置文件提供出去，`Client`通过接口获取数据，并依据此数据初始化自己的应用。

**其实就是 `Server` 端将所有的配置文件服务化，需要配置文件的服务实例去 Config Server 获取对应的数据。将所有的配置文件统一整理，避免了配置文件碎片化。**

如果服务运行期间改变配置文件，服务是不会得到最新的配置信息，需要解决这个问题就需要引入 `Refresh`。可以在服务的运行期间重新加载配置文件。

当所有的配置文件都存储在配置中心的时候，配置中心就成为了一个非常重要的组件。如果配置中心出现问题将会导致灾难性的后果，因此**在生产中建议对配置中心做集群，来支持配置中心高可用性**。

#### 3.4.5、Spring Cloud Bus

上面的 `Refresh` 方案虽然可以解决单个微服务运行期间重载配置信息的问题，但是在真正的实践生产中，**可能会有 N 多的服务需要更新配置，如果每次依靠手动 Refresh 将是一个巨大的工作量**，这时候 Spring Cloud 提出了另外一个解决方案：`Spring Cloud Bus`。

`Spring Cloud Bus` **通过轻量消息代理连接各个分布的节点**。这会**用在广播状态的变化**（例如配置变化）或者其它的消息指令中。`Spring Cloud Bus` 的一个**核心思想是通过分布式的启动器对 Spring Boot 应用进行扩展，也可以用来建立一个或多个应用之间的通信频道**。目前唯一实现的方式是用 `AMQP` 消息代理作为通道。

`Spring Cloud Bus` 是轻量级的通讯组件，也可以用在其它类似的场景中。有了 `Spring Cloud Bus` 之后，当我们改变配置文件提交到版本库中时，会自动的触发对应实例的 `Refresh`，具体的工作流程如下：

![img](.\img\006tNc79ly1fqc5hzoj1qj30hw0bqq38.jpg)

#### 3.4.6、Spring Cloud Zuul

在微服务架构模式下，后端服务的实例数一般是动态的，对于客户端而言很难发现动态改变的服务实例的访问地址信息。因此在基于微服务的项目中为了简化前端的调用逻辑，通常会引入 `API Gateway` 作为轻量级网关，同时 `API Gateway` 中也会实现相关的认证逻辑从而简化内部服务之间相互调用的复杂度。

![img](.\img\006tNc79ly1fqc5ihuft2j30pi0e6mxw.jpg)

Spring Cloud 体系中支持 API Gateway 落地的技术就是 `Zuul`。Spring Cloud Zuul 路由是微服务架构中不可或缺的一部分，**提供动态路由，监控，弹性，安全等的边缘服务**。`Zuul` 是 `Netflix` 出品的一个**基于 JVM 路由和服务端的负载均衡器**。

> 它的具体作用就是服务转发，接收并转发所有内外部的客户端调用。使用 Zuul 可以作为资源的统一访问入口，同时也可以在网关做一些权限校验等类似的功能。

#### 3.4.7、链路跟踪

随着服务越来越多，对调用链的分析会越来越复杂，如服务之间的调用关系、某个请求对应的调用链、调用之间消费的时间等，对这些信息进行监控就成了一个问题。在实际的使用中我们需要监控服务和服务之间通讯的各项指标，这些数据将是我们改进系统架构的主要依据。因此分布式的链路跟踪就变的非常重要，`Spring Cloud` 也给出了具体的解决方案：`Spring Cloud Sleuth` 和 `Zipkin`

![img](.\img\006tNc79ly1fqc5j15b71j30d60cj75i.jpg)

`Spring Cloud Sleuth` 为服务之间调用提供链路追踪。通过 `Sleuth` 可以很清楚的了解到一个服务请求经过了哪些服务，每个服务处理花费了多长时间。从而让我们可以很方便的理清各微服务间的调用关系。

`Zipkin` 是 `Twitter` 的一个开源项目，允许开发者收集 `Twitter` 各个服务上的监控数据，并提供查询接口。

分布式链路跟踪需要 `Sleuth + Zipkin` 结合来实现。

## 4、总结

我们从整体上来看一下 Spring Cloud 各个组件如何来配套使用：

![img](.\img\006tNc79ly1fqc2klep72j30n00jhjsd.jpg)

从上图可以看出 Spring Cloud 各个组件相互配合，合作支持了一套完整的微服务架构。

- 其中 Eureka 负责服务的注册与发现，很好将各服务连接起来
- Hystrix 负责监控服务之间的调用情况，连续多次失败进行熔断保护
- Hystrix dashboard，Turbine 负责监控 Hystrix 的熔断情况，并给予图形化的展示
- Spring Cloud Config 提供了统一的配置中心服务
- 当配置文件发生变化的时候，Spring Cloud Bus 负责通知各服务去获取最新的配置信息
- 所有对外的请求和服务，我们都通过 Zuul 来进行转发，起到 API 网关的作用
- 最后我们使用 Sleuth+Zipkin 将所有的请求数据记录下来，方便我们进行后续分析

# 二、服务治理技术

## 1、Spring Cloud Netflix 的优势

**对于微服务的治理而言，核心就是服务的注册和发现**。所以选择哪个组件，很大程度上要看它对于服务注册与发现的解决方案。在这个领域，开源架构很多，最常见的是 `Zookeeper`，但这并不是一个最佳选择。

在分布式系统领域有个著名的 CAP 定理：**C—— 数据一致性，A—— 服务可用性，P—— 服务对网络分区故障的容错性**。这三个特性在任何分布式系统中不能同时满足，最多同时满足两个。

`Zookeeper` 是著名 `Hadoop` 的一个子项目，很多场景下 `Zookeeper` 也作为 `Service` 发现服务解决方案。**`Zookeeper` 保证的是 CP**，**即任何时刻对 `Zookeeper` 的访问请求能得到一致的数据结果，同时系统对网络分割具备容错性，但是它不能保证每次服务请求的可用性**。从实际情况来分析，在使用 `Zookeeper` 获取服务列表时，如果 zookeeper 正在选主，或者 Zookeeper 集群中半数以上机器不可用，那么将就无法获得数据了。所以说，Zookeeper 不能保证服务可用性。

诚然，对于大多数分布式环境，尤其是涉及到数据存储的场景，数据一致性应该是首先被保证的，这也是 `zookeeper` 设计成 CP 的原因。但是对于服务发现场景来说，情况就不太一样了：针对同一个服务，即使注册中心的不同节点保存的服务提供者信息不尽相同，也并不会造成灾难性的后果。因为对于服务消费者来说，能消费才是最重要的 —— 拿到可能不正确的服务实例信息后尝试消费一下，也好过因为无法获取实例信息而不去消费。**所以，对于服务发现而言，可用性比数据一致性更加重要 ——AP 胜过 CP**。而 **`Spring Cloud Netflix` 在设计 Eureka 时遵守的就是 AP 原则。**

`Eureka` 本身是 `Netflix` 开源的一款提供服务注册和发现的产品，并且提供了相应的 Java 封装。在它的实现中，**节点之间是相互平等的，部分注册中心的节点挂掉也不会对集群造成影响，即使集群只剩一个节点存活，也可以正常提供发现服务**。哪怕是所有的服务注册节点都挂了，**`Eureka Clients` 上也会缓存服务调用的信息(服务实例，IP地址、端口号等)**。这就保证了我们微服务之间的互相调用是足够健壮的。

除此之外，Spring Cloud Netflix 背后强大的开源力量，也促使我们选择了 Spring Cloud Netflix：

- 前文提到过，Spring Cloud 的社区十分活跃，其在业界的应用也十分广泛（尤其是国外），而且整个框架也经受住了 Netflix 严酷生产环境的考验。
- 除了服务注册和发现，Spring Cloud Netflix 的其他功能也十分强大，包括 Ribbon，hystrix，Feign，Zuul 等组件，结合到一起，让服务的调用、路由也变得异常容易。
- Spring Cloud Netflix 作为 Spring 的重量级整合框架，使用它也意味着我们能从 Spring 获取到巨大的便利。Spring Cloud 的其他子项目，比如 Spring Cloud Stream、Spring Cloud Config 等等，都为微服务的各种需求提供了一站式的解决方案。

> Netflix 和 Spring Cloud 是什么关系呢？
> 我第一次 Netflix 这个单词时，是在美剧《纸牌屋》的片头。对，这是一家互联网流媒体播放商，可以这么说该网站上的美剧应该是最火的。由于是美国视频巨头，访问量非常的大，从而促使其技术快速的发展在背后支撑着，也正是如此，Netflix 开始把整体的系统往微服务上迁移。
> 他家的微服务做的不是最早的，但是却是最大规模的在生产级别微服务的尝试。几年前，Netflix 就把它的几乎整个微服务框架栈开源贡献给了社区，叫做 Netflix OSS。
> Spring 背后的 Pivotal 在 2015 年推出的 Spring Cloud 开源产品，主要对 Netflix 开源组件的进一步封装，方便 Spring 开发人员构建微服务基础框架。(虽然 Spring Cloud 到现在为止不只有 Netflix 提供的方案可以集成，还有很多方案，但 Netflix 是最成熟的。)

## 2、Spring Cloud Netflix 主要组件

`Spring Cloud Netflix` 的核心是用于服务注册与发现的 Eureka，接下来我们将以 Eureka 为线索，介绍 `Eureka`、`Ribbon`、`Hystrix`、`Feign` 这些 `Spring Cloud Netfli` 主要组件。

### 2.1、服务注册与发现 Eureka

`Spring Cloud Eureka` 是 Spring Cloud Netflix 微服务套件的一部分，基于 `Netflix Eureka` 做了二次封装，主要负责完成微服务架构中的服务治理功能，**服务治理可以说是微服务架构中最为核心和基础的模块，他主要用来实现各个微服务实例的自动化注册与发现**。

- **服务注册：**在服务治理框架中，通常都会**构建一个注册中心，每个服务单元向注册中心登记自己提供的服务，将主机与端口号、版本号、通信协议等一些附加信息告知注册中心**，注册中心按照服务名分类组织清单，**服务注册中心还需要以心跳的方式去监控清单中的服务是否可用，若不可用需要从清单中剔除**，达到排除故障服务的效果。
- **服务发现：**由于在服务治理框架下运行，**服务间的调用不再通过指定具体的实例地址来调用，而是通过向服务名发起请求调用实现**。

`Spring Cloud Eureka` 使用 `Netflix Eureka` 来实现服务注册与发现，即包括了服务端组件，也包含了客户端组件，并且服务端和客户端均采用 Java 编写，所以 Eureka 主要适用与通过 Java 实现的分布式系统，或是与 JVM 兼容语言构建的系统，但是，由于 Eureka 服务端的服务治理机制提供了完备的 RESTful API，所以他也支持将非 Java 语言构建的微服务纳入 Eureka 的服务治理体系中来。

Eureka由多个服务实例组成，这些服务实例可以分为两种：`Eureka Server` 和 `Eureka Client`。为了便于理解，我们将 `Eureka Client`再分为`Service Provider` 和 `Service Consumer`。如下图所示：

![img](.\img\006tNc79ly1fqc78zi0lpj30fa08hjrj.jpg)

- **Eureka Server**：服务的注册中心，负责维护注册的服务列表，同其他服务注册中心一样，支持高可用配置。
- **Service Provider**：服务提供方，作为一个 Eureka Client，向 Eureka Server 做服务注册、续约和下线等操作，注册的主要数据包括服务名、机器 ip、端口号、域名等等。
- **Service Consumer**：服务消费方，作为一个 Eureka Client，向 Eureka Server 获取 Service Provider 的注册信息，并通过远程调用与 Service Provider 进行通信。

**举个例子：**

- 3y跟女朋友去东站的东方宝泰逛街，但不知道东方宝泰有什么好玩的。于是就去**物业**搜了一下**东方宝泰商户清单**，发现一楼有优衣库，二楼有星巴克，三楼有麦当劳。
- 在优衣库旁边，有新开张的KFC，在墙壁打上了很大的标识“欢迎KFC**入驻**东方宝泰”。
- 商家们需要定时**交物业费**给物业。
- **物业维持**东方宝泰的稳定性。如果某个商家不想在东方宝泰运营了，告诉了物业。物业自然就会将其在东方宝泰商户清单去除。

> Service Provider 和 Service Consumer 不是严格的概念，Service Consumer 也可以随时向 Eureka Server 注册，来让自己变成一个 Service Provider。
>
> Spring Cloud 针对服务注册与发现，进行了一层抽象，并提供了三种实现：Eureka、Consul、Zookeeper。目前支持得最好的就是 Eureka，其次是 Consul，最后是 Zookeeper。在层抽象的作用下，我们可以无缝地切换服务治理实现，并且不影响任何其他的服务注册、服务发现、服务调用等逻辑。

#### 2.1.1、Eureka Server

`Eureka Server` 作为一个独立的部署单元，**以 `REST API` 的形式为服务实例提供了注册、管理和查询等操作**。同时，`Eureka Server` 也为我们提供了可视化的监控页面，可以直观地看到各个 `Eureka Server` 当前的运行状态和所有已注册服务的情况。

##### 2.1.1.1、Eureka Server 的高可用集群

`Eureka Server` 可以运行多个实例来构建集群，解决单点问题，但不同于 `ZooKeeper` 的选举 `leader` 的过程，**`Eureka Server` 采用的是 `Peer to Peer` 对等通信。这是一种去中心化的架构，无 `master/slave` 区分，每一个 `Peer` 都是对等的**。在这种架构中，**节点通过彼此互相注册来提高可用性，每个节点需要添加一个或多个有效的 `serviceUrl` 指向其他节点**。每个节点都可被视为其他节点的副本。

**如果某台 `Eureka Server` 宕机，`Eureka Client` 的请求会自动切换到新的 `Eureka Server` 节点，当宕机的服务器重新恢复后，`Eureka` 会再次将其纳入到服务器集群管理之中**。当节点开始接受客户端请求时，所有的操作都会进行 `replicateToPeer`（节点间复制）操作，将请求复制到其他 `Eureka Server` 当前所知的所有节点中。

**一个新的`Eureka Server`节点启动后，会首先尝试从邻近节点获取所有实例注册表信息，完成初始化**。`Eureka Server`通过`getEurekaServiceUrls()`方法获取所有的节点，并且会**通过心跳续约的方式定期更新**。默认配置下，如果**`Eureka Service`在一定的时间内没有接收到某个服务实例的心跳，`Eureka Server`将会注销该实例**（**默认为90秒**，通过 `eureka.instance.lease-expiration-duration-in-seconds` 配置）。**当 Eureka Server 节点在短时间内丢失过多的心跳时（比如发生了网络分区故障），那么这个节点就会进入自我保护模式**。下图为 `Eureka` 官网的架构图

![img](.\img\006tNc79ly1fqc7gr97yuj30k00f03zi.jpg)

##### 2.1.1.2、自我保护模式

如果在 Eureka Server 的首页看到以下这段提示，则说明 Eureka 已经进入了保护模式。

> EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY’RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.

**默认配置下，如果 `Eureka Server` 每分钟收到心跳续约的数量低于一个阈值，并且持续 15 分钟，就会触发自我保护。**

```
阈值 = instance的数量 × (60 / instance的心跳间隔秒数) × 自我保护系数
```

**在自我保护模式中，`Eureka Server` 会保护服务注册表中的信息，不再注销任何服务实例**。**当它收到的心跳数重新恢复到阈值以上时，该 `Eureka Server` 节点就会自动退出自我保护模式**。它的设计哲学前面提到过，那就是**宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例**。这样做会使客户端很容易拿到实际已经不存在的服务实例，会出现调用失败的情况。因此客户端要有容错机制，比如请求重试、断路器。该模式可以通过 `eureka.server.enable-self-preservation = false` 来禁用，~~同时 `eureka.instance.lease-renewal-interval-in-seconds` 可以用来更改心跳间隔（默认 30s），~~`eureka.server.renewal-percent-threshold` 可以用来修改自我保护系数（默认 0.85）。

**特别注意**
上边的那个阈值理论上是那么计算的，但是在实际中，并不是！！！并不是！！！并不是！！！
因为实际代码中是硬编码了，每个注册的实例都是**直接 + 2**，看下边 `eureka-core-1.8.7.jar!com.netflix.eureka.registry.AbstractInstanceRegistry#register` 中的这段代码就明白了

```java
// The lease does not exist and hence it is a new registration
synchronized (lock) {
    if (this.expectedNumberOfRenewsPerMin > 0) {
        // Since the client wants to cancel it, reduce the threshold
        // (1
        // for 30 seconds, 2 for a minute)
        this.expectedNumberOfRenewsPerMin = this.expectedNumberOfRenewsPerMin + 2;
        this.numberOfRenewsPerMinThreshold =
                (int) (this.expectedNumberOfRenewsPerMin * serverConfig.getRenewalPercentThreshold());
    }
}
```

##### 2.1.1.3、Eureka Server 的 Region、Zone

Region、Zone、Eureka 集群三者的关系，如下图所示：
![img](.\img\006tNc79ly1fqc7it32axj30by09m0sm.jpg)

`region` 和 `zone`（或者 `Availability Zone`）均是亚马逊网络服务 (AWS) 的概念。在非 AWS 环境下，我们可以先简单地**将 `region` 理解为 `Eureka` 集群，`zone` 理解成机房。上图就可以理解为一个 `Eureka` 集群被部署在了 `zone1` 机房和 `zone2` 机房中**。

##### 2.1.1.4、Service Provider

###### 服务注册

`Service Provider` 本质上是一个 `Eureka Client`。它启动时，会调用服务注册方法，向 `Eureka Server` 注册自己的信息。`Eureka Server` 会维护一个已注册服务的列表，这个列表为一个嵌套的 `HashMap`：

```java
private final ConcurrentHashMap<String, Map<String, Lease<InstanceInfo>>> registry
        = new ConcurrentHashMap<String, Map<String, Lease<InstanceInfo>>>();
```

- 第一层，application name 和对应的服务实例。
- 第二层，服务实例及其对应的注册信息，包括IP，端口号等。

当实例状态发生变化时（如自身检测认为 Down 的时候），也会向 Eureka Server 更新自己的服务状态，同时用 `replicateToPeers()` 向其它 Eureka Server 节点做状态同步。

![img](.\img\006tNc79ly1fqc7qck6kdj30kw0c8glu.jpg)

###### 续约与剔除

前面提到过，**服务实例启动后，会周期性地向 `Eureka Server` 发送心跳以续约自己的信息，避免自己的注册信息被剔除。续约的方式与服务注册基本一致：首先更新自身状态，再同步到其它 Peer**。

如果 Eureka Server 在一段时间（90秒）内没有接收到某个微服务节点的心跳，Eureka Server 将会注销该微服务节点（自我保护模式除外）。

![img](.\img\006tNc79ly1fqc7qonmikj30kv0cxglq.jpg)

##### 2.1.1.5、Service Consumer

`Service Consumer` 本质上也是一个 `Eureka Client`（它也会向 `Eureka Server` 注册，只是这个注册信息无关紧要罢了）。它**启动后，会从`Eureka Server`获取所有实例的注册信息，包括IP地址、端口号等，并缓存到本地。这些信息默认每30秒更新一次**。前文提到过，如果与 `Eureka Server` 通信中断，`Service Consumer` 仍然可以通过本地缓存与 `Service Provider` 通信。

实际开发 Eureka 的过程中，有时会遇见 Service Consumer 获取到 Server Provider 的信息有延迟，在 [Eureka Wiki](https://github.com/Netflix/eureka/wiki/Understanding-eureka-client-server-communication) 中有这么一段话:

> All operations from Eureka client may take some time to reflect in the Eureka servers and subsequently in other Eureka clients. This is because of the caching of the payload on the eureka server which is refreshed periodically to reflect new information. Eureka clients also fetch deltas periodically. Hence, **it may take up to 2 mins for changes to propagate to all Eureka clients.**

最后一句话提到，服务端的更改可能需要 2 分钟才能传播到所有客户端，至于原因并没有介绍。**这是因为 Eureka 有三处缓存和一处延迟造成的。**

- `Eureka Server` 对注册列表进行缓存，默认时间为 30s。
- `Eureka Client` 对获取到的注册信息进行缓存，默认时间为 30s。
- `Ribbon` 会从上面提到的 `Eureka Client` 获取服务列表，将负载均衡后的结果缓存 30s。
- 如果不是在 Spring Cloud 环境下使用这些组件 (`Eureka`, `Ribbon`)，服务启动后并不会马上向 Eureka 注册，而是需要等到第一次发送心跳请求时才会注册。心跳请求的发送间隔默认是 30s。Spring Cloud 对此做了修改，服务启动后会马上注册。

基于 `Service Consumer` 获取到的服务实例信息，我们就可以进行服务调用了。而 Spring Cloud 也为 Service Consumer 提供了丰富的服务调用工具：

- Ribbon，实现客户端的负载均衡。
- Hystrix，断路器。
- Feign，RESTful Web Service 客户端，整合了 Ribbon 和 Hystrix。

### 2.2、服务调用端负载均衡 ——Ribbon

`Ribbon` 是 Netflix 发布的开源项目，**主要功能是为 `REST客户端`实现负载均衡**。它主要包括六个组件：

-  **`ServerList`**，负载均衡使用的服务器列表。这个列表会缓存在负载均衡器中，并定期更新。当`Ribbon`与`Eureka`结合使用时，`ServerList`的实现类就是`DiscoveryEnabledNIWSServerList`，它会保存 `Eureka Server` 中注册的服务实例表。

- **`ServerListFilter`**，服务器列表过滤器。这是一个接口，主要用于对 Service Consumer 获取到的服务器列表进行预过滤，过滤的结果也是 ServerList。Ribbon 提供了多种过滤器的实现。

- **`IPing`**，探测服务实例是否存活的策略。

- **`IRule`**，负载均衡策略，其实现类表述的策略包括：轮询、随机、根据响应时间加权等，其类结构如下图所示。

  ![img](.\img\006tNc79ly1fqc7ssqkaxj30mj0alq3y.jpg)

  > 我们也可以自己定义负载均衡策略，比如我们就利用自己实现的策略，实现了服务的版本控制和直连配置。实现好之后，将实现类重新注入到 `Ribbon` 中即可。

- **`ILoadBalancer`**，负载均衡器。这也是一个接口，`Ribbon` 为其提供了多个实现，比如 `ZoneAwareLoadBalancer`。而**上层代码通过调用其 API 进行服务调用的负载均衡选择**。一般 `ILoadBalancer` 的实现类中会引用一个 `IRule`。

- **`RestClient`**，服务调用器。顾名思义，这就是负载均衡后，Ribbon 向 Service Provider 发起 REST 请求的工具。

**`Ribbon` 工作时会做四件事情：**

1. 优先选择在同一个 `Zone` 且负载较少的 `Eureka Server`；
2. 定期从 `Eureka` 更新并过滤服务实例列表；
3. 根据用户指定的策略，在从 Server 取到的服务注册列表中选择一个实例的地址；
4. 通过 `RestClient` 进行服务调用。

### 2.3、服务调用端熔断 ——Hystrix

#### 2.3.1、雪崩效应

> **在微服务架构中通常会有多个服务层调用，基础服务的故障可能会导致级联故障，进而造成整个系统不可用的情况，这种现象被称为服务雪崩效应**。服务雪崩效应是一种因 “服务提供者” 的不可用导致 “服务消费者” 的不可用，并将不可用逐渐放大的过程。

如果下图所示：A 作为服务提供者，B 为 A 的服务消费者，C 和 D 是 B 的服务消费者。A 不可用引起了 B 的不可用，并将不可用像滚雪球一样放大到 C 和 D 时，雪崩效应就形成了。

![img](.\img\006tNc79ly1fqdrhznd6yj30ak0cyaep.jpg)

##### 2.3.1.1、断路器

> Netflix 创建了一个名为 `Hystrix` 的库，实现了断路器的模式。**“断路器” 本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩**。

![img](.\img\006tNc79ly1fqc7u3f954j30fs09vgln.jpg)

> 当然，**在请求失败频率较低的情况下，`Hystrix` 还是会直接把故障返回给客户端**。只**有当失败次数达到阈值时，断路器打开并且不进行后续通信，而是直接返回备选响应**。当然，`Hystrix` 的备选响应也是可以由开发者定制的。

![img](.\img\006tNc79ly1fqc7ubeb0uj30kz0drdg8.jpg)

##### 2.3.1.2、监控

> 除了隔离依赖服务的调用以外，`Hystrix` 还提供了准实时的调用监控（**`Hystrix Dashboard`**），`Hystrix` 会持续地记录所有通过 Hystrix 发起的请求的执行信息，并以统计报表和图形的形式展示给用户，包括每秒执行多少请求多少成功，多少失败等。Netflix 通过 `hystrix-metrics-event-stream` 项目实现了对以上指标的监控。Spring Cloud 也提供了 `Hystrix Dashboard` 的整合，对监控内容转化成可视化界面，[Hystrix Dashboard Wiki](https://github.com/Netflix/Hystrix/wiki/Dashboard) 上详细说明了图上每个指标的含义。

![img](.\img\006tNc79ly1fqc7ukr4mmj30hs0bf0v6.jpg)

### 2.4、服务调用端代码抽象和封装 ——Feign

`Feign`是一个声明式的`Web Service`客户端，它的目的就是让`Web Service`调用更简单。它整合了`Ribbon`和`Hystrix`，从而让我们不再显示地使用这两个组件。`Feign`还提供了HTTP请求的模板，通过编写简单的接口和插入注解，我们就可以定义好HTTP请求参数、格式、地址等信息。接下来，`Feign`会完全代理HTTP的请求，我们只需要像调用方法一样调用它就可以完成服务请求。

**Feign具有如下特性：**

- 可插拔的注解支持，包括`Feign`注解和`JAS-RX`注解。
- 支持可插拔的HTTP编码器和解码器。
- 支持`Hystrix`和它的`fallback`。
- 支持Ribbon的负载均衡。
- 支持HTTP请求和响应的压缩。

## 3、服务注册与发现 Eureka

### 3.1、服务注册中心

#### 3.1.1、注册中心的意义

管理各种服务功能包括服务的注册、发现、熔断、负载、降级等，比如dubbo admin后台的各种功能。

有了注册中心，调用关系的变化，画几个简图来看一下。

服务A调用服务B



[![SpringCloud系列教程 | 第二篇：注册中心Eureka](.\img\simple.png)](https://springcloud-oss.oss-cn-shanghai.aliyuncs.com/chapter2/simple.png?raw=true)



有了注册中心之后，任何一个服务都不在是直连的，都需要通过注册中心去调用。



[![SpringCloud系列教程 | 第二篇：注册中心Eureka](.\img\simple_register_center.png)](https://springcloud-oss.oss-cn-shanghai.aliyuncs.com/chapter2/simple_register_center.png?raw=true)



如果是一个连续调用：

服务A调用服务B，服务B调用服务C



[![SpringCloud系列教程 | 第二篇：注册中心Eureka](.\img\simple_two.png)](https://springcloud-oss.oss-cn-shanghai.aliyuncs.com/chapter2/simple_two.png?raw=true)



这里如果加上注册中心，整个调用流程就会分为两步，服务A先从注册中心请求服务B，服务B再从注册中心请求服务C



[![SpringCloud系列教程 | 第二篇：注册中心Eureka](.\img\simple_two_register_center.png)](https://springcloud-oss.oss-cn-shanghai.aliyuncs.com/chapter2/simple_two_register_center.png?raw=true)



上面的示例只是描述了两三个服务之间的互相调用，可能加上注册中心还会稍显繁琐，如果一条调用链上面有几十个服务（这个丝毫不是开玩笑哦，正常的业务流程中很可能出现这种复杂的调用过程），在工作中我就遇到过超过20个服务的互相调用，这种复杂业务场景的互相调用，如果不使用注册中心，画出来的图会连成一个网状结构，单从图上面已经很难找出服务的上下游关系了。其中如果一个服务有改动，就会牵扯到上下游多台机器的重启，整个架构设计完全耦合在一起，每次改动所需要的工作量完全超出想象。通过注册中心去注册服务，完全不在需要关心上下游机器的ip地址，由几台服务器组成，是否重启才会生效，注册中心已经帮我们把服务的注册和发现做好了，我们只需要知道注册中心在哪里，对应的服务名是什么就ok啦~~

由于各种服务都注册到了服务中心，就有了去做很多高级功能条件。比如几台服务提供相同服务来做均衡负载；监控服务器调用成功率来做熔断，移除服务列表中的故障点；监控服务调用时间来对不同的服务器设置不同的权重等等。

#### 3.1.2、案例实践

先创建一个父工程，然后后续的工程都以这个工程为父，实现maven的聚合。这样可以在一个窗口看到所有工程。**在实际开发中，每个微服务可独立一个工程**。

##### 3.1.2.1、创建父工程

![img](.\img\20191112143005545.png)

![img](.\img\20191112143108976.png)

![img](.\img\20191112143433798.png)

![在这里插入图片描述](.\img\2019111214381391.png)

![img](.\img\20191112143955484.png)

**修改主工程的pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 子工程引用，创建子项目后在此添加即可 -->
    <modules>
		
    </modules>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>com.jjcc</groupId>
    <artifactId>springcloud-study</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>springcloud-study</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

- **parent：** 父级依赖项目，这里能看到依赖的父级的springboot的版本是2.1.6.RELEASE。
- **properties：** 当前配置文件一些配置，可以看到Java的版本是1.8，springcloud的版本是Greenwich.SR1。
- **dependencies：** 当前项目依赖的组件，这里能看出来依赖两个组件，一个是Eureka，还有一个是test测试框架。
- **dependencyManagement：** 这里是声明依赖，并不实现引入，如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom;另外如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。
- **build：** 在build中声明了当前使用的插件，spring-boot-maven-plugin，主要功能：能够将Spring Boot应用打包为可执行的jar或war文件，然后以通常的方式运行Spring Boot应用。

父工程创建完成。

##### 3.1.2.2、创建子工程服务注册中心

**用于注册各种微服务，以便于其他微服务找到和访问。**

![在这里插入图片描述](.\img\20191113154527536.png)

![在这里插入图片描述](.\img\20191113153236626.png)

![在这里插入图片描述](.\img\20191113154805404.png)

**修改子工程服务注册的pom文件**

创建完后的工程，其pom.xml继承了父pom文件，并引入`spring-cloud-starter-netflix-eureka-server`的依赖，代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.jjcc</groupId>
        <artifactId>springcloud-study</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>

    <groupId>com.jjcc.cloud</groupId>
    <artifactId>eureka-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>eureka-server</name>
    <description>Demo project for Spring Boot</description>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>
</project>

```

**编辑 application.yml**

eureka是一个高可用的组件，它没有后端缓存，每一个实例注册之后需要向注册中心发送心跳（因此可以在内存中完成），在默认情况下erureka server也是一个eureka client ,必须要指定一个 server。eureka server的配置文件appication.yml：

```yml
server:
  #  注册中心的端口
  port: 8761

# 注册中心配置
eureka:
  server:
    # 关闭Eureka的自我保护模式；建议只在开发环境中使用
    enable-self-preservation: false
  instance:
    # 主机名称
    hostname: localhost
  client:
    # 是否注册到服务器。因为他本身就是服务器，所以无需注册到服务器
    register-with-eureka: false
    # 是否获取注册中心的注册信息
    fetch-registry: false
    serviceUrl:
      # http://${eureka.instance.hostname}:${server.port}/eureka/ 自己作为服务器，公布出来的地址。
      # 比如后续某个微服务要把自己注册到 eureka server, 那么就要使用这个地址：http://localhost:8761/eureka/
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

spring:
  application:
    # 应用名称，会在Eureka中作为服务的id标识（serviceId）
    name: eureka-server
```

- **eureka.client.register-with-eureka：**

  表示是否将自己注册到`Eureka Server`，默认为 true。

- **eureka.client.fetch-register：**

  表示是否从`Eureka Server`获取注册信息，默认为ture。

- **eureka.client.serviceUrl.defaultZone：**

  设置与Eureka Server交互的地址，查询服务和注册服务都需要依赖这个地址。默认是http://localhost:8761/eureka ；多个地址可使用英文逗号（,）分隔。

- **spring.application.name：**

  应用名称，会在Eureka中作为服务的id标识（serviceId）

##### 3.1.2.3、启动服务注册中心

通过 `@EnableEurekaServer` 注解启动一个服务注册中心提供给其他应用进行对话。只需要在一个普通的 Spring Boot 应用中添加这个注解就能开启此功能。

```java
package com.springcloud.Eureka;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }

}
```

增加注解`@EnableEurekaServer`，在这个main函数上，直接右键debug就可以启动了，访问 http://127.0.0.1:8761/ ，启动成功如下图所示：

![在这里插入图片描述](.\img\20191113160736906.png)

> No application available 没有服务被发现 ……_
> 因为没有注册服务当然不可能有服务被发现了。

### 3.2、Eureka Server集群

注册中心这么关键的服务，如果是单点话，遇到故障就是毁灭性的。**在一个分布式系统中，服务注册中心是最重要的基础部分，理应随时处于可以提供服务的状态。为了维持其可用性，使用集群是很好的解决方案**。**Eureka 通过互相注册的方式来实现高可用的部署，所以我们只需要将 `Eureke Server` 配置其他可用的 `service-url` 就能实现高可用部署**。

多个Eureka Server之间也会互相注册为服务，**当服务提供者注册到Eureka Server集群中的某个节点时，该节点会把务的信息同步给集群中的每个节点，从而实现数据同步**。因此，无论客户端访问到Eureka Server集群中的任意一个节点，都可以获取到完整的服务列表信息。

而作为客户端，需要把信息注册到每个Eureka中：

![image-20200108093818940](.\img\image-20200108093818940.png)

如果有三个Eureka，则每一个EurekaServer都需要注册到其它几个Eureka服务中，例如：有三个分别为7000、
7001、7002，则：

- 7000要注册到7001和7002上
- 7001要注册到7000和7002上
- 7002要注册到7000和7001上

**1、修改原来的EurekaServer配置**

```yml
server:
  #  注册中心的端口
  port: 7000

# 注册中心配置
eureka:
  server:
    # 关闭注册中心的自我保护模式。建议只在开发环境中开启。；
    # 每分钟收到的心跳续约量少于一定的阈值，并且持续15分钟，就会触发自我保护模式
    # 自我保护模式下，会保护服务注册表的信息，不再注销任何服务实例。
    enable-self-preservation: false
  instance:
    # 主机名称
    hostname: localhost
  client:
    # 是否注册到服务器，因为它本身就是服务器，所需无需注册到服务器。
    # 做集群需要设置为true
    register-with-eureka: true
    # 是否获取注册中心的注册信息
    # Eureka Server做集群需要设置为true
    fetch-registry: true
    serviceUrl:
      # 自己作为服务器，公布出来的地址。后续微服务要把自己注册到Eureka Server，需要访问这个地址。
      defaultZone: http://127.0.0.1:7001/eureka/, http://127.0.0.1:7002/eureka/

spring:
  application:
    # 微服务的名称。
    name: eureka-server
```

**所谓的高可用注册中心，其实就是把`EurekaServer`自己也作为一个服务，注册到其它`EurekaServer`上，这样多个`EurekaServer`之间就能互相发现对方，从而形成集群**。因此我们做了以下修改：

> 把`register-with-eureka`和`fetch-registry`修改为true或者注释掉

**2、新建的多个EurekaServer配置**

```yml
server:
  port: 7001

spring:
  application:
    # 微服务名称，会在Eureka中作为服务的标识（serviceId）
    name: eureka-server

eureka:
  instance:
    hostname: localhost
  server:
    # 是否开启自我保护模式
    enable-self-preservation: false
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://127.0.0.1:7000/eureka/, http://127.0.0.1:7002/eureka/
```

```yml
server:
  port: 7002

spring:
  application:
    # 微服务名称，会在Eureka中作为服务的标识（serviceId）
    name: eureka-server

eureka:
  instance:
    hostname: localhost
  server:
    # 是否开启自我保护模式
    enable-self-preservation: false
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://127.0.0.1:7000/eureka/, http://127.0.0.1:7001/eureka/
```

**3、启动测试**

![img](.\img\idea-config.png)

在刚启动第一个`Eureka Server`的时候，启动完成后会在控制台看到一些异常信息，大致就是拒绝连接、请求超时这一类的，这个不用管，启动后续`Eureka Server`后就好了。

依次启动完成后，访问 http://localhost:7001/，效果如下:

![img](.\img\006tKfTcly1fr2l67mm1bj30sg0iktbu.jpg)

根据图可以看出 peer1 的注册中心 DS Replicas 已经有了 peer2 的相关配置信息，并且出现在 available-replicas 中。我们手动停止 peer2 来观察，发现 peer2 就会移动到 unavailable-replicas 一栏中，表示 peer2 不可用。

![img](.\img\006tKfTcly1fr2kyw47ndj30sg0i1q5q.jpg)

到此双节点的配置已经完成。

**注意事项**

-  在搭建 Eureka Server 双节点或集群的时候，**要把 `eureka.client.register-with-eureka` 和 `eureka.client.fetch-registry` 均改为 `true`（默认）。否则会出现实例列表为空，且 peer2 不在 available-replicas 而在 unavailable-replicas 的情况（这时其实只是启动了两个单点实例）**。

- **在注册的时候，配置文件中的 `spring.application.name` 必须一致**，否则情况会是这样的

  ![img](.\img\006tKfTcly1fr2l2jh5hmj30sg0izq65.jpg)

## 4、服务提供与调用 Eureka

案例中有三个角色：服务注册中心、服务提供者、服务消费者，其中服务注册中心就是前面的Eureka server单节点启动即可。

流程如下：

1. 启动注册中心
2. 服务提供者生产服务并注册到服务中心中。
3. 消费者从服务中心中获取服务实例并调用服务提供者执行。

![img](.\img\006tNc79ly1fqdhgdesulj30fa08hjrj.jpg)

### 4.1、服务提供者

当client向server注册时，它会提供一些元数据，例如主机和端口，URL，主页等。Eureka server 从每个client实例接收心跳消息。 如果心跳超时，则通常将该实例从注册server中删除。

#### 4.1.1、POM 包配置

创建过程同`Eureka Server`类似,创建完pom.xml如下：

```xml
   <parent>
        <groupId>com.jjcc</groupId>
        <artifactId>springcloud-study</artifactId>
        <version>0.0.1-SNAPSHOT</version>

    </parent>

    <groupId>com.jjcc.provider</groupId>
    <artifactId>eureka-client-provider-two</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>eureka-client-provider-two</name>
    <description>Demo project for Spring Boot</description>


    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

#### 4.1.2、配置文件application.yml

```yml
spring:
  application:
    name: eureka-producer
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7000/eureka/
server:
  port: 8000
```

通过`spring.application.name`属性，**我们可以指定微服务的名称后续在调用的时候只需要使用该名称就可以进行服务访问**。`eureka.client.service-url.defaultZone`属性对应服务注册中心的配置内容，指定服务注册中心的位置。

#### 4.1.3、启动类

保持默认生成的即可，`Finchley.RC1` 这个版本的 Spring Cloud开始已经无需添加`@EnableDisccoveryClient`注解了。（如果引入的相关的jar包，又想禁用服务注册与发现功能，设置`eureka.client.enabled=false`）

```java
@SpringBootApplication
public class EurekaClientProviderTwoApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaClientProviderTwoApplication.class, args);
    }

}
```

#### 4.1.4、Controller

提供 hello 服务

```java
@RestController
@RequestMapping(value = "/hello")
public class HelloController {

    @GetMapping("/{name}")
    public String hello(@PathVariable String name) {
        return "Hello，" + name + "。当前时间：" + LocalDateTime.now();
    }


}
```

启动工程后，就可以在注册中心 [Eureka](http://localhost:7000/) 的页面看到 `EUERKA-PRODUCER` 服务。
![img](.\img\006tKfTcly1fr2lk7o8mhj30sg044wfd.jpg)

### 4.2、服务消费者

在微服务架构中，业务都会被拆分成一个独立的服务，服务与服务的通讯是基于http restful的。Spring cloud有两种服务调用方式，一种是`ribbon+restTemplate`，另一种是`feign`。实际使用中用的应该都是 `Feign`。

`Ribbon`它是一个基于 HTTP 和 TCP 的客户端负载均衡器。它**可以通过在客户端中配置 `ribbonServerList` 来设置服务端列表去轮询访问以达到均衡负载的作用**。

当 `Ribbon` 与 `Eureka` 联合使用时，`ribbonServerList` 会被 `DiscoveryEnabledNIWSServerList` 重写，扩展成从 Eureka 注册中心中获取服务实例列表。同时它也会用 NIWSDiscoveryPing 来取代 IPing，它将职责委托给 Eureka 来确定服务端是否已经启动。

#### 4.2.1、ribbon+restTemplate

##### 4.2.1.1、POM 包配置

在它的pom.xml继承了父pom文件，并引入了以下依赖：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.jjcc</groupId>
        <artifactId>springcloud-study</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>

    <groupId>com.jjcc.consummer</groupId>
    <artifactId>eureka-consummer-one</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>eureka-consummer-one</name>
    <description>Demo project for Spring Boot</description>


    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    
</project>
```

Spring Cloud 版本 Finchley.RC1开始，`spring-cloud-starter-netflix-eureka-client` 里边已经包含了 `spring-cloud-starter-netflix-ribbon` 了。

##### 4.2.1.2、配置文件

```yml
server:
  port: ${port:8000}

spring:
  application:
    name: eureka-produce

eureka:
  client:
    service-url:
      # 指向Eureka Server注册中心的地址
      defaultZone: http://localhost:7000/eureka/

```

##### 4.2.1.3、启动类

在工程的启动类中无需添加`@EnableDiscoveryClient`注解，`Finchley.RC1`版本开始随服务自动启动。

通过`@LoadBalanced`注解表明这个`restRemplate`开启负载均衡的功能。

```java
@SpringBootApplication
public class EurekaConsummerOneApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaConsummerOneApplication.class, args);
    }
    
    /**
     * @LoadBalanced注解表明这个RestTemplate开启负载均衡的功能。
     * @title restTemplate
     * @author Jjcc
     * @return org.springframework.web.client.RestTemplate
     * @createTime 2020/1/8 14:17
     */
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

##### 4.2.1.4、Controller

通过之前注入IOC容器的`restTemplate`来消费 `server-provider`服务的 "/hello" 接口。

```java
@RestController
@RequestMapping("/hello")
public class HelloController {

    private RestTemplate restTemplate;

    @Autowired
    public HelloController(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    @GetMapping("/{name}")
    public String hello(@PathVariable String name) {
        String url = "http://eureka-produce/hello/" + name;
        return restTemplate.getForObject(url, String.class);
    }
}
```

这里用服务名`eureka-produce`取代了具体的`host:port`。在`ribbon`中，`ribbon`有一个拦截器，拦截到请求后，它会根据服务名来选择具体的服务实例，并将这里的服务名替换成实际要求的IP地址和端口号，从而完成服务接口的调用。

在浏览器多次访问 http://localhost:9000/hello/windmt 

```
Hello, windmt! Fri Apr 13 22:24:14 CST 2018：8000
Hello, windmt! Fri Apr 13 22:24:14 CST 2018：8001
```

这说明当我们通过调用`restTemplate.getForObject(url, String.class)`方法时，已经做了负载均衡，访问了不同的端口的服务实例。

##### 4.2.1.5、此时的架构

![201908021002_2.png](.\img\201908021002_2.png)

- 一个服务注册中心，`Eureka Server`端口号为8761
- 两个服务提供方，`Service-hi`端口分别为8762、8763，分别向服务中心注册
- 一个服务消费者，`sercvice-ribbon`端口为8764,向服务注册中心注册
- `server-ribbon`通过`restTemplate`调用service-hi的接口时，因为用Ribbon实现了负载均衡，会轮流的调用service-hi：8762和8763 两个端口的hi接口；

##### 启动多个服务方式

![image-20200108164120841](.\img\image-20200108164120841.png)

![image-20200108164201061](.\img\image-20200108164201061.png)

![image-20200108164312087](.\img\image-20200108164312087.png)

**springboot配置信息外部大于内部，这里重新指定一个端口号，保存后，启动即可。**

#### 4.2.2、Spring Cloud Feign

`Feign`是一个声明式的伪http客户端，它使得Http客户端变得更简单。**使用Feign，只需要创建一个接口并注解**。它具有可插拔的注解特性，可使用`Feign`和`JAX-RX`注解。Feign支持可插拔的编码器和解码器。Feign默认集成了`Ribbon`和`Hystrix`，并和Eureka结合，默认实现了负载均衡的效果。

简而言之：

- Feign采用的是基于接口的注解
- Feign整合了Ribbon，具有负载均衡的功能
- 整合了Hystrix，具有熔断的能力

##### 4.2.2.1、POM 包配置

创建一个springboot项目，在它的pom文件引入Feign的起步依赖`spring-cloud-starter-feign`、Eureka的起步依赖`spring-cloud-starter-netflix-eureka-client`、Web的起步依赖`spring-boot-starter-web`

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

##### 4.2.2.2、配置文件

指定程序名为service-consumer，端口号为9001，服务注册地址为http://127.0.0.1/7000/eureka/ 

```yml
server:
  port: 9000

spring:
  application:
    name: eureka-consumer

eureka:
  client:
    service-url:
      deufaltZone: http://127.0.0.1/7000/eureka/
```

##### 4.2.2.3、启动类

在启动类上添加`@EnableFeignClient`注解开启Feign功能。

- `@EnableFeignClient`：springboot在启动的时候，会扫描被`@FeignClent`注解的类。
- `@FeignClient`：在进行远程服务调用的时候会用到。

```java
/**
 * @EnableFeign 这个注解是通知Springboot在启动的时候扫描被@FeignClient修饰的类。
 * @FeignClient 这个注解在进行远程调用的时候会用到。
 * @author Jjcc
 */
@EnableFeignClients
@SpringBootApplication
public class EurekaConsumerFeignApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaConsumerFeignApplication.class, args);
    }

}
```

##### 4.2.2.4、Feign 调用实现

Feign是一个声明式`Web Service`客户端。**使用Feign能让编写Web Service客户端更加简单，它的使用方法是定义一个接口，然后在上面添加注解，同时也支持`JAX-RX`标准的注解**。Feign也支持可插拔式的编码器和解码器。Spring Cloud对Feign进行了封装，使其支持了Spring MVC标准注解和HttpMessageConverters。Feign可以与Eureka和Ribbon组合使用以支持负载均衡。

创建一个 `Feign` 的客户端接口定义。**使用 `@FeignClient` 注解来指定这个接口所要调用的服务名称，接口中定义的各个函数使用 Spring MVC 的注解就可以来绑定服务提供方的 REST 接口**，比如下面就是绑定 eureka-producer 服务的 `/hello/` 接口的例子：

```java
@Feign(name = "eureka-produce")
public interface HelloRemote {

    ("/hello/")
    String hello(@RequestParam(value = "name") String name);

}
```

**此类中的方法和服务提供者中 Contoller 中的方法名和参数需保持一致。**

##### 4.2.2.5、Controller



















