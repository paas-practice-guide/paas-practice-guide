# **第6章：从资源管理扩展的PaaS框架—Mesos&Maranthon**

Mesos起初是加州大学伯克利分校的Benjamin Hindman, Andy Konwinski, Matei Zaharia, Ali Ghodsi, Anthony D. Joseph, Randy Katz, Scott Shenker, and Ion Stoica发起的一个研究项目，之后Benjamin把Mesos引入到Twitter来支持Twitter的数据中心运行，再后来Mesos成为Mesosphere公司DCOS的主体基础架构。同时Mesos在****年成为Apache的顶级项目，和Yarn类似作为分布式系统资源管理框架，比如Mesos的校友—Spark在早期除了stand alone部署模式外，就可以运行在Mesos之上。Spark on Yarn是后来的事情了，但目前却成了stand alone模式以外的主流。

## Mesos系统概览

Mesos系统的设计初衷是用来进行集群资源管理和调度使应用可以方便在一个弹性集群中部署以及调度运行，通常我们把能够运行在Mesos系统上的一个应用称为一个Meos框架（FrameWork），为了能够为集群中的框架提供各式各样的资源，Mesos一方面通过使用一些内核技术，例如Linux的cgroups、Solaris的zones，来提供CPU、内存、IO、文件系统的隔离，Meos颇有创新的引入了被称为“resource offers“的分布式两层调度机制，这样Mesos可以决定有多少集群可以可以提供给每个框架，同时框架会决定接受和使用多少资源。Mesos系统通过像各框架提供通用的集群资源获取接口可以完成对整个集群资源的细粒度调配，同时运行在Mesos系统上的应用又可以共享整个Mesos集群的资源，这样就达到了将多种分布式系统运行Mesos系统上，进而提高了集群资源的使用效率。

### Mesos系统的架构

![Mesos Architecture](https://assets.digitalocean.com/articles/mesosphere/mesos_architecture.png)

**Note:** "ZK" represents ZooKeeper in this diagram.

一个Mesos系统包含Mesos master、Mesos slave daemons以及一系列的frameworks. 上面的架构图显示了一个Mesos系统的构成，其中

- **Master 进程**: 运行在Master节点上，用来管理slave节点
- **Slave 进程**: 运行Master和Salve节点上，用来运行由Framework产生的任务
- **Framework**: 也被称为Mesos应用,包含调度器和执行器，调度器会接受来自master的offers，执行器负责启动slave节点上的任务，常见的Mesos框架有Marathon、Chronos还有Hadoop。
- **Offer**: 实际上是一份slave节点上可用内存、CPU资源的列表，Slave节点报告可用资源给Master，Master向已注册的框架提供资源信息。
- **Task**:一组有Framwork调度到Slave节点上执行的工作，任务可以是一个操作系统命令、可以是一个脚本、可以是一个SQL语言甚至可以是一个Hadoop的job。
- **Apache ZooKeeper**: 用来完成多个Master之间的协调。

通过如上图所示的架构，Mesos就可以对整个集群及应用进行底层的资源管理，在Mesos Master上会配置每个FrameWork可以使用的资源配额，每个Framework的调度器会确定使用配额中的哪一部分资源，一旦调度器决定使用一部分资源，那么调度器会同时Mesos执行哪个任务，接下来Mesos会在适当的slave上启动任务，当任务结束以后资源会被释放，然后可以被mesos重新调度给其他framwork使用。

下面我们来重点看看Mesos系统的几个关键点是如何完成:

- **资源到底是如何供给的**
- **资源又是如何隔离的**
- **两层调度是如何完成的**

**资源供给**，我们说Mesos slave节点会向Master报告可用资源，比如某个Mesos slave节点向Master节点报告自己的可用内存为16GB、可用CPU为8，可用存储空间为64GB。同时我们假设当前Mesos系统中运行着FrameworkA和FramworkB，Mesos会首先FramworkA的调度器提供资源，FramworkA接受了其中的一半资源，即4CPUs、8G内存以及32G可用存储空间，接着Mesos将剩下的资源提供给FramworkB，如下图所示：
[![mesos (1)](http://eugenedvorkin.com/wp-content/uploads/2015/07/mesos-1.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/mesos-1.png)

**资源隔离.** Mesos在集群顶层给每个Framwork分配了相应的资源，那么在每个节点上又是如何去运行不用的任务呢？Mesos在Slave节点可以通过内置的基于cgroup的容器或者Docker容器来实现进程隔离，这样就可以让不同应用运行在一个Slave节点上又互不影响，如下图所示，Mesos还可以支持定制化的容器
[![isolation](http://eugenedvorkin.com/wp-content/uploads/2015/07/isolation.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/isolation.png)

最后我们来看看 **两层调度是如何分配资源的**. 之前我们说Mesos Master从Mesos Slave获取了所有可用资源信息，进而基于资源的分配策略，Mesos Master将这些资源提供给不同的Framwork，在这里Mesos是使用一种叫“Weighted Dominant Resource Fairness algorithm”的算法（详见《Dominant Resource Fairness: Fair Allocation of Multiple Resource Types》，http://static.usenix.org/events/nsdi11/tech/full_papers/Ghodsi.pdf)，来决定每个Framework能获得哪些资源，我们可以通过调整权重来改变Mesos的资源分配结果，比如“ –weights prod:30, QA:20, dev:10”，这样我们就可以让Mesos将30%的资源分配给prod，将20%的资源分配给QA。

[![drf](http://eugenedvorkin.com/wp-content/uploads/2015/07/drf.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/drf.png)



下面是一些已经支持Mesos的应用或框架:

**常驻进程**

- **Aurora** is a service scheduler that runs on top of Mesos, enabling you to run long-running services that take advantage of Mesos' scalability, fault-tolerance, and resource isolation.
- **Marathon** is a private PaaS built on Mesos. It automatically handles hardware or software failures and ensures that an app is "always on."
- **Singularity** is a scheduler (HTTP API and web interface) for running Mesos tasks: long running processes, one-off tasks, and scheduled jobs.
- **SSSP** is a simple web application that provides a white-label "Megaupload" for storing and sharing files in S3.

**大数据分析**

- **Cray Chapel** is a productive parallel programming language. The Chapel Mesos scheduler lets you run Chapel programs on Mesos.
- **Dpark** is a Python clone of Spark, a MapReduce-like framework written in Python, running on Mesos.
- **Exelixi** is a distributed framework for running genetic algorithms at scale.
- **Hadoop :** Running Hadoop on Mesos distributes MapReduce jobs efficiently across an entire cluster.
- **Hama** is a distributed computing framework based on Bulk Synchronous Parallel computing techniques for massive scientific computations e.g., matrix, graph and network algorithms.
- **MPI** is a message-passing system designed to function on a wide variety of parallel computers.
- **Spark** is a fast and general-purpose cluster computing system which makes parallel jobs easy to write.
- **Storm** is a distributed realtime computation system. Storm makes it easy to reliably process unbounded streams of data, doing for realtime processing what Hadoop did for batch processing.

**批处理任务**

- **Chronos** is a distributed job scheduler that supports complex job topologies. It can be used as a more fault-tolerant replacement for cron.
- **Jenkins** is a continuous integration server. The mesos-jenkins plugin allows it to dynamically launch workers on a Mesos cluster depending on the workload.
- **JobServer** is a distributed job scheduler and processor which allows developers to build custom batch processing Tasklets using point and click web UI.
- **Torque** is a distributed resource manager providing control over batch jobs and distributed compute nodes.

**数据存储**

- **Cassandra** is a highly available distributed database. Linear scalability and proven fault-tolerance on commodity hardware or cloud infrastructure make it the perfect platform for mission-critical data.
- **ElasticSearch** is a distributed search engine. Mesos makes it easy to run and scale.
- **Hypertable** is a high performance, scalable, distributed storage and processing system for structured and unstructured data.

通过上面的应用列表我们可以总结出那些应用比较适合在Mesos系统中运行

[![use-mesos](http://eugenedvorkin.com/wp-content/uploads/2015/07/use-mesos.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/use-mesos.png)

对于那些有状态或者单体的应用以及那些需要持久化数据到磁盘上的应用并不是非常合适运行在Mesos系统中。

通过以上对Mesos的介绍可以看出Mesos本身只是负责资源管理的一个框架或者系统，可以看出Mesos本身并不是为PAAS平台设计的，但是Mesos灵活的资源管理和调度能力使得它有一个非常不错的基础，我们也看到Mesos应用中有一些例如Marathon这样的常驻进程编排框架、Chronos这样的通用批处理任务框架、Jenkins这样的应用开发流水线工具框架，结合这些框架提供的能力，我们就可以基于Mesos系统来搭建一套PAAS平台。我们通过Mesos来进行集群资源管理，通过Marathon来进行微服务应用的编排和管理。也就是DCOS所完成的任务。

## Marathon简介

前面几节我们介绍过Mesos系统的一些运行原理，只有Mesos框架可以直接运行在Mesos上，接收并使用Mesos的分配的资源，那么是不是所有的应用，特别是大量的传统三层架构业务应用，都需要改造成Mesos框架呢，答案是否定的，我们可以借助像**Marathon**这样的通用常驻进程框架来直接运行我们的应用，更进一步我们除了可以借助Marathon的能力方便的对应用进行启停外，我们还可以通过Marathon来实现应用的高可用，当一个应用实例被异常终止后，Marathon可以在集群的其他节点上重新启动应用实例。

[![marathon](http://eugenedvorkin.com/wp-content/uploads/2015/07/marathon.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/marathon.png)

Marathon还可以完成应用健康检查、服务自愈、服务发现、节点指定、可编程API以及WEB操作界面等功能，具体功能如下：

- [High Availability](https://mesosphere.github.io/marathon/docs/high-availability.html). Marathon runs as an active/passive cluster with leader election for 100% uptime.
- Multiple container runtimes. Marathon has first-class support for both [Mesos containers](https://mesosphere.github.io/marathon/docs/application-basics.html) (using cgroups) and [Docker](https://mesosphere.github.io/marathon/docs/native-docker.html).
- [Stateful apps](https://mesosphere.github.io/marathon/docs/persistent-volumes.html). Marathon can bind persistent storage volumes to your application. You can run databases like MySQL and Postgres, and have storage accounted for by Mesos.
- [Beautiful and powerful UI](https://mesosphere.github.io/marathon/docs/marathon-ui.html).
- [Constraints](https://mesosphere.github.io/marathon/docs/constraints.html). e.g. Only one instance of an application per rack, node, etc.
- [Service Discovery & Load Balancing](https://mesosphere.github.io/marathon/docs/service-discovery-load-balancing.html). Several methods available.
- [Health Checks](https://mesosphere.github.io/marathon/docs/health-checks.html). Evaluate your application's health using HTTP or TCP checks.
- [Event Subscription](https://mesosphere.github.io/marathon/docs/rest-api.html#event-subscriptions). Supply an HTTP endpoint to receive notifications - for example to integrate with an external load balancer.
- [Metrics](https://mesosphere.github.io/marathon/docs/metrics.html). Query them at /metrics in JSON format or push them to systems like graphite, statsd and Datadog.
- [Complete REST API](https://mesosphere.github.io/marathon/docs/rest-api.html) for easy integration and scriptability.


### 使用Marathon编排应用和框架

下面的图显示了通过Marathon在Mesos中同时编排其他框架和服务

![img](https://mesosphere.github.io/marathon/img/architecture.png)

在Mesos集群中Marathon第一个被启动的框架，然后Marathon的调度器就开始被当做操作系统的 `init`, `upstart`进程使用,在这里我们通过Marathon启动了两个Chronos调度器，这样Marathon就可以在Chronos调度器应为某些原因失败以后把他们重新启动起来，在Chronos调度器被启动起来以后，它们就可以接收来自Mesos Master的资源供给，我们看到他们在Mesos系统上又启动了两个任务，一个用来备份数据库，另一个用来发送电子邮件。同时Marathon会继续启动其他类型的应用容器，通过Docker容器或者Mesos容器，比如JBoss servers, Jetty, Sinatra, Rails等。