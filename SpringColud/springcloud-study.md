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

修改 Controller，将 `HelloRemote` 注入到 controller 层，像普通方法一样去调用即可

```java
@RestController
@RequestMapping("/hello")
public class HelloController {

    private IHelloRemote helloRemote;

    @Autowired
    public HelloController(IHelloRemote iHelloRemote) {
        this.helloRemote = iHelloRemote;
    }

    @GetMapping("/{name}")
    public String hello(@PathVariable String name) {
        return helloRemote.hello(name);
    }
}
```

通过Spring Cloud Feign来实现服务调用的方式非常简单，**通过`@FeignClient` 定义的接口来统一声明我们需要依赖的微服务接口。而在具体使用的时候就跟调用本地方法一样的进行调用即可。由于Feign是基于Ribbon实现了，所以它自带了负载均衡功能，也可以通过Ribbon的IRule进行策略扩展**。另外，Feign 还整合的 Hystrix 来实现服务的容错保护。

要想使用 Feign，至少需要以下三个依赖

- `spring-boot-starter-web`
- `spring-cloud-starter-openfeign`
- `spring-cloud-starter-netflix-eureka-client`

## 5、 服务容错保护 Hystrix

### 5.1、什么是服务熔断？

#### 5.1.1、简介

熔断这一概念来源于电子工程中的断路器（`Circuit Breaker`）。**在互联网系统中，当下游服务因访问压力过大而响应变慢或失败，上游服务为了保证系统的可用性，可以暂时切断对下游服务的访问**。

> **这种牺牲局部，保全整体的措施就叫熔断**。

如果不采取熔断措施，我们的系统会怎样呢？我们来看一个栗子。

当前系统中有A，B，C三个服务，服务A是上游，服务B是中游，服务C是下游。它们的调用链如下：

![img](.\img\20180507103456708.png)

一旦下游服务C因某些原因变得不可用，积压了大量请求，服务B的请求线程也随之阻塞。线程资源逐渐耗尽，使得服务B也变得不可用。紧接着，服务A也变为不可用，整个调用链路被拖垮。

![img](.\img\20180507103502138.png)

像这种调用链路的连锁故障，叫做雪崩。

正所谓刮骨疗毒，壮士断腕。在这种时候，就需要我们的熔断机制来挽救整个系统。熔断机制的大体流程和刚才所讲的考试策略如出一辙：

![img](.\img\20180507103507143.png)

**这里需要解释两点**：

1. 开启熔断
   在快照时间窗内（`最近的10s`），接口调用超时比率达到一个阈值（快照时间窗内请求总数大于指定数`20次`，请求失败率超过指定的阈值 `50%`），会开启熔断。进入熔断状态后，后续对该服务接口的调用不在经过网络，直接执行本地的默认方法（`fallback`），达到服务降级的效果。
2. 熔断恢复
   熔断不可能是永久的。当经过了规定时间之后（`5s`），服务将从熔断状态恢复过来，再次接受调用方的调用。

#### 5.1.2、服务熔断的实际应用

`Spring Cloud Hystrix`是基于`NetFlix`的开源框架`Hystrix`实现，该框架实现了熔断服务、线程池隔离等一系列服务保护功能。

对于熔断机制的实现，Hystrix设计了三种状态：

1. 熔断关闭状态（`Closed`）
   服务没有故障时，熔断器所处的状态，对调用方的调用不做任何限制。
2. 熔断开启状态（`Open`）
   在快照时间窗口内（Hystrix默认是`10s`），接口调用错误比率达到一个阈值（默认为`50%`），会进入熔断开启状态。进入熔断状态后，后续对该服务的调用不再经过网络，直接执行本地的`fallback`方法。
3. 熔断半开状态（`Half-Open`）
   在进入熔断开启状态一段时间后（默认为`5s`），熔断器会进入半熔断状态。所谓半熔断就是尝试恢复服务调用，允许有限的流量调用该服务，并监控调用成功率。如果成功率达到阈值，则说明服务已恢复，进入熔断关闭状态；如果成功率仍旧很低，则重新进入熔断开启状态。

![img](.\img\20180507103529239.png)

### 5.2、什么是服务降级？

#### 5.2.1、简介

**当服务压力剧增的情况下，根据实际业务情况及流量，`对一些服务和页面有策略的不处理或换种简单的方式处理，从而释放服务资源以保证核心服务正常运作或高效运作`**。

> 假如目前有很多人想要给我付钱，但我的服务器除了正在运行支付的服务之外，还有一些其它的服务在运行，比如搜索、定时任务和详情等等。然而这些不重要的服务就占用了JVM的不少内存与CPU资源，为了能把钱都收下来（钱才是目标），我设计了一个动态开关，把这些不重要的服务直接在最外层拒掉，这样处理后的后端处理收钱的服务就有更多的资源来收钱了（收钱速度更快了），这就是一个简单的服务降级的使用场景。

#### 5.2.2、使用场景

**当整个微服务架构整体的负载超出了预设的上限阈值或即将到来的流量预计将会超过预设的阈值时，为了保证重要或基本的服务能正常运行，可以将一些不重要或不紧急的服务或任务进行服务的 `延迟使用` 和 `暂停使用`**。

#### 5.2.3、核心设计

##### 5.2.3.1、分布式开关

根据上述需求，我们可以设置一个分布式开关，用于实现服务的降级，然后集中式管理开关配置信息即可。具体方案如下：

![img](.\img\p)

##### 5.2.3.2、自动降级

**超时降级**

主要配置好超时时间和超时重试次数和机制，并使用异步机制探测恢复情况。

**失败次数降级**

主要是一些不稳定的API，当失败调用次数达到一定阀值自动降级，同样要使用异步机制探测回复情况。

**故障降级**

如果调用的远程服务挂掉了（网络故障、DNS故障、Http服务返回错误的状态码和RPC服务抛出异常），则可以直接降级。

**限流降级**

当触发了限流超额时，可以使用暂时屏蔽的方式来进行短暂的屏蔽。

> 当我们去秒杀或者抢购一些限购商品时，此时**`可能会因为访问量太大而导致系统崩溃，此时开发者会使用限流来进行限制访问量，当达到限流阀值，后续请求会被降级`**；降级后的处理方案可以是：排队页面（将用户导流到排队页面等一会重试）、无货（直接告知用户没货了）、错误页（如活动太火爆了，稍后重试）。

##### 5.2.3.3、服务降级后的应对策略

当触发服务降级后，新的请求再次到达时，我们该如何来处理这些请求呢？从微服务架构全局的视角来看，通常有以下是几种常用的降级处理方案：

- 页面降级
  可视化页面禁用点击按钮，调整静态页面。
- 延迟服务
  如定时任务延迟处理、消息如MQ后延迟处理。
- 写降级
  直接禁止相关写操做的请求。
- 读降级
  直接禁止读相关操作的请求。
- 缓存降级
  使用缓存方式来降级部分读频繁的服务接口。

针对后端代码层面的降级处理策略，则我们通常使用以下几种处理措施进行降级处理：

- 抛异常
- 返回NULL
- 调用`Mock`数据
- 调用`fallback`处理逻辑

### 5.3、服务雪崩效应

#### 5.3.1、定义

服务雪崩效应是一种因**服务提供者**不可用而导致**服务调用者**不可用，并将**不可用逐渐放大**的过程。如图所示：

![img](.\img\006tNc79ly1fqdrhznd6yj30ak0cyaep-1578644990218.jpg)

上图中，A 为服务提供者，B 为 A 的服务调用者，C 和 D 是 B 的服务调用者。当 A 的不可用，引起 B 的不可用，并将不可用逐渐放大 C 和 D 时，服务雪崩就形成了。

#### 5.3.2、形成的原因

把服务雪崩的参与者简化为**服务提供者**和**服务调用者**，并将服务雪崩产生的过程分为以下三个阶段来分析形成的原因：

1. 服务提供者不可用
2. 重试加大流量
3. 服务调用者不可用

![img](.\img\006tNc79ly1fqdriwoonkj30ak0e3gpl.jpg)

服务雪崩的每个阶段都可能由不同的原因造成，比如造成**服务提供者不可用**的原因有：

- 硬件故障
  硬件故障可能为硬件损坏造成的服务器主机宕机，网络硬件故障造成的服务提供者的不可访问。
- 程序Bug
  程序BUG导致服务调用者远程调用后，提供方抛出异常且无响应，造成线程长时间阻塞，随着访问线程的积累，造成服务不可用。
- 缓存击穿
  **缓存击穿一般发生在缓存应用重启，所有缓存被清空时以及短时间内大量缓存失效时。大量的缓存不命中，使请求直击后端，造成服务提供者超负荷运行**，引起服务不可用。
- 用户大量请求
  在秒杀和大促销开始前，如果准备不充分，用户发起大量请求也会造成服务提供者的不可用。

而形成 **重试加大流量** 的原因有:

-  用户重试
  在服务提供者不可用后，用户由于忍受不了界面上长时间的等待，而不断刷新页面甚至提交表单。
- 代码逻辑重试
  服务调用端的会存在大量服务异常后的重试逻辑。

这些重试都会进一步加大请求流量。

**服务调用者不可用** 产生的主要原因是:

- 同步等待造成的资源耗尽
  当服务调用者使用 **同步调用** 时，会产生大量的等待线程占用系统资源。一旦线程资源被耗尽，服务调用者提供的服务也将处于不可用。

#### 5.3.3、应对策略

针对造成服务雪崩的不同原因，可以使用不同的对应策略：

1. 流量控制
2. 改进缓存机制
3. 服务自动扩容
4. 服务调用者降级服务

**流量控制** 的具体措施包括：

- 网关限流
- 用户交互限流
- 关闭重试

因为 Nginx 的高性能，目前一线互联网公司大量采用 `Nginx+Lua` 的网关进行流量控制，由此而来的 `OpenResty` 也越来越热门。

**用户交互限流** 的具体措施有：

1. 采用加载动画，提高用户的容忍等待时间。
2. 提交按钮添加强制等待时间机制。

**改进缓存模式** 的措施包括:

-  缓存预加载
- 同步改为异步刷新

**服务自动扩容** 的措施主要有:

- AWS 的 auto scaling

防止**服务调用者降级** 的措施包括：

- 资源隔离
  资源隔离**主要是对调用服务的线程池进行隔离**。它会**为每一个依赖服务创建一个独立的线程池**，这样就算某个依赖服务出现延迟过高的情况(长时间占用线程，随着请求积累，导致资源耗尽)，也只是对该依赖服务产生影响，而不会拖慢其它的依赖服务。
- 对依赖服务进行分类
  我们根据具体业务，**将依赖服务分为：强依赖和弱依赖。强依赖服务不可用会导致当前业务中止，而弱依赖服务的不可用不会导致当前业务的中止**。
- 不可用服务的调用快速失败(`fallback` 失败快速返回)
  不可用服务的调用快速失败一般通过**超时机制**，**熔断器** 和熔断后的 **降级方法**来实现。当某个服务单元发生故障（类似用电器发生短路）之后，通过断路器的故障监控（类似熔断保险丝）， **向调用方返回一个错误响应， 而不是长时间的等待**。这样就不会使得线程因调用故障服务被长时间占用不释放，**避免**了故障在分布式系统中的**蔓延**。

### 5.4、使用Hystrix预防服务雪崩

#### 5.4.1、快速响应失败（FallBack）

对于查询操作，我们可以实现一个`fallback`方法，当请求后端服务出现异常的时候，可以使用`fallback`方法返回的值，**向调用方返回一个错误响应，而不是长时间的等待**。这样就不会使得线程因调用故障服务被长时间占用不释放，避免了故障在分布式系统中的蔓延。`fallback`方法一般是设置的默认值或者来自缓存。

#### 5.4.2、资源隔离

货船为了进行防止漏水和火灾的扩散，会将货仓分隔为多个，如下图所示:

![img](.\img\006tNc79ly1fqdrrru8tcj30ja08ytgf.jpg)

这种资源隔离减少风险的方式称为：`Bulkheads`(**舱壁隔离模式**);

`Hystrix`将同样的模式运用到了服务调用这上。

在`Hystrix`中，主要通过线程池来实现资源隔离。通常**在使用的时候会根据调用的远程服务划分出多个线程池**。例如调用产品服务的 Command 放入 A 线程池，调用账户服务的 Command 放入 B 线程池。这样做的**主要优点是运行环境被隔离开了**。这样**就算调用服务的代码存在 bug 或者由于其他原因导致自己所在线程池被耗尽时，不会对系统的其他服务造成影响**。

**通过对依赖服务的线程池隔离实现，可以带来如下优势**：

- **应用自身得到完全的保护，不会受不可控的依赖服务影响**。即便给依赖服务分配的线程池被填满，也不会影响应用自身的其余部分。
- **可以有效的降低接入新服务的风险**。如果新服务接入后运行不稳定或存在问题，完全不会影响到应用其他的请求。
- 当依赖的服务从失效恢复正常后，它的线程池会被清理并且能够马上恢复健康的服务，相比之下容器级别的清理恢复速度要慢得多。
- 当依赖的服务出现配置错误的时候，线程池会快速的反应出此问题（通过失败次数、延迟、超时、拒绝等指标的增加情况）。同时，我们可以在不影响应用功能的情况下通过实时的动态属性刷新（后续会通过 Spring Cloud Config 与 Spring Cloud Bus 的联合使用来介绍）来处理它。
- 当依赖的服务因实现机制调整等原因造成其性能出现很大变化的时候，此时线程池的监控指标信息会反映出这样的变化。同时，我们也可以通过实时动态刷新自身应用对依赖服务的阈值进行调整以适应依赖方的改变。
- **除了上面通过线程池隔离服务发挥的优点之外，每个专有线程池都提供了内置的并发实现，可以利用它为同步的依赖服务构建异步的访问**。

通过对依赖服务实现线程池隔离，让我们的应用更加健壮，**不会因为个别依赖服务出现问题而引起非相关服务的异常**。同时，也使得我们的应用变得更加灵活，**可以在不停止服务的情况下，配合动态配置刷新实现性能配置上的调整**。

虽然线程池隔离的方案带了如此多的好处，但是很多使用者可能会担心为每一个依赖服务都分配一个线程池是否会过多地增加系统的负载和开销。对于这一点，使用者不用过于担心，因为这些顾虑也是大部分工程师们会考虑到的，Netflix 在设计 Hystrix 的时候，认为线程池上的开销相对于隔离所带来的好处是无法比拟的。同时，Netflix 也针对线程池的开销做了相关的测试，以证明和打消 Hystrix 实现对性能影响的顾虑。

下图是 Netflix Hystrix 官方提供的一个 Hystrix 命令的性能监控，该命令以每秒 60 个请求的速度（QPS）向一个单服务实例进行访问，该服务实例每秒运行的线程数峰值为 350 个。

![img](.\img\006tNc79ly1fqdppn9wwgj30pr0fymyd.jpg)

从图中的统计我们可以看到，使用线程池隔离与不使用线程池隔离的耗时差异如下表所示：

| 比较情况  | 未使用线程池隔离 | 使用了线程池隔离 | 耗时差距 |
| :-------- | :--------------- | :--------------- | :------- |
| 中位数    | 2ms              | 2ms              | 2ms      |
| 90 百分位 | 5ms              | 8ms              | 3ms      |
| 99 百分位 | 28ms             | 37ms             | 9ms      |

 在 99% 的情况下，使用线程池隔离的延迟有 9ms，对于大多数需求来说这样的消耗是微乎其微的，更何况为系统在稳定性和灵活性上所带来的巨大提升。**虽然对于大部分的请求我们可以忽略线程池的额外开销，而对于小部分延迟本身就非常小的请求（可能只需要 1ms），那么 9ms 的延迟开销还是非常昂贵的。实际上 Hystrix 也为此设计了另外的一个解决方案：信号量（`Semaphores`）**。

**`Hystrix` 中除了使用线程池之外，还可以使用信号量来控制单个依赖服务的并发度，信号量的开销要远比线程池的开销小得多，但是它不能设置超时和实现异步访问**。所以，只有在依赖服务是足够可靠的情况下才使用信号量。在 `HystrixCommand` 和 `HystrixObservableCommand` 中 2 处支持信号量的使用：

- 命令执行：如果隔离策略参数 `execution.isolation.strategy` 设置为 `SEMAPHORE`，`Hystrix` 会使用信号量替代线程池来控制依赖服务的并发控制。
- 降级逻辑：当 `Hystrix` 尝试降级逻辑时候，它会在调用线程中使用信号量。

信号量的默认值为 10，我们也可以通过动态刷新配置的方式来控制并发线程的数量。对于信号量大小的估算方法与线程池并发度的估算类似。仅访问内存数据的请求一般耗时在 1ms 以内，性能可以达到 5000rps，这样级别的请求我们可以将信号量设置为 1 或者 2，我们可以按此标准并根据实际请求耗时来设置信号量。

#### 5.4.3、断路器模式

断路器模式源于 `Martin Fowler` 的 [Circuit Breaker](https://martinfowler.com/bliki/CircuitBreaker.html) 一文。“断路器” 本身是一种开关装置，用于在电路上保护线路过载，当线路中有电器发生短路时，“断路器” 能够及时的切断故障电路，防止发生过载、发热、甚至起火等严重后果。

在分布式架构中，断路器模式的作用也是类似的，**当某个服务单元发生故障（类似用电器发生短路）之后，通过断路器的故障监控（类似熔断保险丝），直接切断原来的主逻辑调用**。但是，在 `Hystrix` 中的断路器除了切断主逻辑的功能之外，还有更复杂的逻辑，下面我们来看看它更为深层次的处理逻辑。

断路器开关相互转换的逻辑如下图：

![img](.\img\006tNc79ly1fqdp243wfbj30dv06zwgz.jpg)

当 `Hystrix Command` **请求后端服务失败数量超过一定阈值，断路器会切换到开路状态 (Open)。这时所有请求会直接失败而不会发送到后端服务（执行`fallback`）**。

> 这个阈值涉及到三个重要参数：**`快照时间窗`**、**`请求总数下限`**、**`错误百分比下限`**。这个参数的作用分别是：
>
> - 快照时间窗：**断路器确定是否打开需要统计一些请求和错误数据，而统计的时间范围就是快照时间窗，默认为最近的 10 秒**。
> - 请求总数下限：**在快照时间窗内，必须满足请求总数下限才有资格进行熔断。默认为 `20`，意味着在 10 秒内，如果该 `Hystrix Command` 的调用此时不足 `20` 次，即时所有的请求都超时或其他原因失败，断路器都不会打开**。
> - 错误百分比下限：**当请求总数在快照时间窗内超过了下限次数，比如发生了 `30` 次调用，如果在这 `30` 次调用中，有 `16` 次发生了超时异常或其它异常，也就是超过 `50%` 的错误百分比，在默认设定 `50%` 下限情况下，这时候就会将断路器打开**。
>
> **断路器保持在开路状态一段时间后 (默认 `5 秒`)，自动切换到半开路状态 (`HALF-OPEN`)。这时会判断下一次请求的返回情况，如果请求成功，断路器切回闭路状态 (`CLOSED`)，否则重新切换到开路状态 (`OPEN`)**。

简而言之就是；当`hystrix Command`请求后端在指定的时间范围内请求数达到了请求总数下限，且请求失败数量超过一定比率（默认`50%`），断路器会切到开路状态（`OPEN`）。这时请求会直接失败，而不会发送到后端服务（`fallback`）。断路器在保持开路状态一段时间后（默认5秒），自动切换到半开路状态（`HALF-OPEN`）。这时下一次请求不会直接返回错误响应，而是会判断请求的返回情况，如果请求成功，断路器切回闭路状态(`CLOSED`)，否则重新切换到开路状态（`OPEN`）。

`Hystrix`提供几个熔断关键参数：**`滑动窗口大小（10s）、请求总数下限（20个）、熔断器开关间隔（5s）、错误率（50%）`**

- 10秒中内每当20个请求中，有50%失败时，熔断器就会打开，此时再调用此服务，将会**直接返回失败**，不再调远程服务。
- 直到5s钟之后（**断路器半开状态**），重新检测该触发条件，**判断是否把熔断器关闭，或者继续打开**。

Hystrix还有请求合并、请求缓存这样强大的功能。

### 5.5、使用 Feign Hystrix

因为熔断只是作用在服务调用这一端，因此我们根据上一篇的示例代码只需要改动 `eureka-consumer-feign` 项目相关代码就可以。

#### 5.5.1、POM 配置

因为 `Feign` 中已经依赖了 `Hystrix` 所以在 `maven` 配置上不用做任何改动。

#### 5.5.2、配置文件

在原来的 application.yml 配置的基础上修改；其中新增了`feign.hystrix.enabled = true`

```yml
spring:
  application:
    name: eureka-consumer
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7000/eureka/ # 访问服务注册中心的链接
server:
  port: 9000 # 服务端口号

feign:
  hystrix:
    # feign开启熔断
    enabled: true

```

#### 5.5.3、创建回调类

创建`HelloRemoteImpl`类实现`IHelloRemoteImpl`中实现回调的方法（`fallback`方法）。

```java
/**
 * fallback方法
 * @author Jjcc
 * @version 1.0.0
 * @className HelloRemoteImpl.java
 * @createTime 2020年01月14日 15:42:00
 */
@Component
public class HelloRemoteImpl implements IHelloRemote {
    @Override
    public String hello(String name) {
        return "Hello World！！！";
    }
}
```

#### 5.5.4、添加 fallback 属性

在 `IHelloRemote` 类添加指定 `fallback` 类，在服务熔断的时候返回 `fallback` 类中的内容。

```java
/**
 * feign通过创建一个接口并通过@FeignClient注解实现远程调用
 * @author Jjcc
 * @version 1.0.0
 * @className IHelloRemote.java
 * @createTime 2020年01月09日 22:58:00
 */
@FeignClient(name = "eureka-produce", fallback = HelloRemoteImpl.class)
public interface IHelloRemote {

    /**
     * 远程服务调用test
     * @title hello
     * @author Jjcc
     * @param name 参数
     * @return java.lang.String
     * @createTime 2020/1/9 23:01
     */
    @GetMapping("hello/{name}")
    String hello(@PathVariable String name);
}
```

#### 5.5.5、测试

依次启动 eureka-server、eureka-producer 和刚刚的 eureka-consumer-hystrix 这三个项目。

访问：http://localhost:9003/hello/windmt
返回：`[0]Hello, windmt! Sun Apr 15 23:14:25 CST 2018`

说明加入 Hystrix 后，不影响正常的访问。接下来我们手动停止 eureka-producer 项目再次测试：

访问：http://localhost:9003/hello/windmt
返回：`Hello World!`

这时候我们再次启动 eureka-producer 项目进行测试：

访问：http://localhost:9003/hello/windmt
返回：`[0]Hello, windmt! Sun Apr 15 23:14:52 CST 2018`

根据返回结果说明熔断成功。

> **通过使用 `Hystrix`，我们能方便的防止雪崩效应，同时使系统具有自动降级和自动恢复服务的效果。**

## 6、Hystrix 监控面板

[Hystrix 监控面板](https://windmt.com/2018/04/16/spring-cloud-5-hystrix-dashboard/)

[熔断监控Hystrix Dashboard和Turbine](https://www.geekdigging.com/2019/08/31/2746365378/)

[断路器监控(Hystrix Dashboard)](http://cmsblogs.com/?p=5551)

## 7、Hystrix 监控数据聚合

[Hystrix 监控数据聚合 Turbine](https://windmt.com/2018/04/17/spring-cloud-6-turbine/)

[熔断监控Hystrix Dashboard和Turbine](https://www.geekdigging.com/2019/08/31/2746365378/)

[断路器聚合监控(Hystrix Turbine)](http://cmsblogs.com/?p=5553)

































