# **第6章：从资源管理扩展的PaaS框架—Mesos&Maranthon**

Mesos起初是加州大学伯克利分校的Benjamin Hindman, Andy Konwinski, Matei Zaharia, Ali Ghodsi, Anthony D. Joseph, Randy Katz, Scott Shenker, and Ion Stoica发起的一个研究项目，之后Benjamin把Mesos引入到Twitter来支持Twitter的数据中心运行，再后来Mesos成为Mesosphere公司DCOS的主体基础架构。同时Mesos在****年成为Apache的顶级项目，和Yarn类似作为分布式系统资源管理框架，比如Mesos的校友—Spark在早期除了stand alone部署模式外，就可以运行在Mesos之上。Spark on Yarn虽然是后来的事情了，但目前却成了stand alone模式以外的主流。

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



通过以上对Mesos的介绍可以看出Mesos本身只是负责资源管理的一个框架或者系统，它本身并不是为PAAS平台设计的，但是Mesos灵活的资源管理和调度能力使得它有一个非常不错的基础，我们也看到Mesos应用中有一些例如Marathon这样的常驻进程编排框架、Chronos这样的通用批处理任务框架、Jenkins这样的应用开发流水线工具框架，结合这些框架提供的能力，我们就可以基于Mesos系统来搭建一套PAAS平台。我们通过Mesos来进行集群资源管理，通过Marathon来进行微服务应用的编排和管理。这也就是DCOS所完成的任务。

## Marathon简介

前面几节我们介绍过Mesos系统的一些运行原理，所以我们知道只有Mesos框架可以直接运行在Mesos上，接收并使用Mesos的分配的资源，那么是不是所有的应用，特别是大量的传统三层架构业务应用，都需要改造成Mesos框架呢，答案是否定的，我们可以借助像**Marathon**这样的通用常驻进程框架来直接运行我们的应用，更进一步我们除了可以借助Marathon的能力方便的对应用进行启停外，我们还可以通过Marathon来实现应用的高可用，当一个应用实例被异常终止后，Marathon可以在集群的其他节点上重新启动应用实例。

下面的图显示了通过Marathon在Mesos中同时编排其他框架和服务

![img](https://mesosphere.github.io/marathon/img/architecture.png)

在Mesos集群中Marathon第一个被启动的框架，然后Marathon的调度器就开始被当做操作系统的 `init`, `upstart`进程使用,在这里我们通过Marathon启动了两个Chronos调度器，这样Marathon就可以在Chronos调度器应为某些原因失败以后把他们重新启动起来，在Chronos调度器被启动起来以后，它们就可以接收来自Mesos Master的资源供给，我们看到他们在Mesos系统上又启动了两个任务，一个用来备份数据库，另一个用来发送电子邮件。同时Marathon会继续启动其他类型的应用容器，通过Docker容器或者Mesos容器，比如JBoss servers, Jetty, Sinatra, Rails等。

## **Mesos Marathon运行时环境**

由于Mesos Marathon还是完成基于容器的调度和编排，因此Mesos Marathon方案不会对运行时有特殊要求。

## **Mesos Marathon服务发现与路由**

在Mesos Marathon方案中通常可以通过三种方式来完成服务发现和路由。

第一是使用Mesos-DNS，Mesos-DNS会给包括Marathon框架在内的每个Mesos任务生成一个SRV记录，在SRV记录中会记录每个应用的主机或SDN IP和端口，这样我们只需要记住要访问的服务名称就可以通过Mesos-DNS方便的查找到对应的IP和端口。因此Mesos-DNS非常适用于在Mesos集群中用多个框架进行调度和编排的情况，还有通过SDN来管理容器IP以及通过Marathon为应用分配随机端口的情况。

Mesos-DNS的设计上采取了轻量化和无状态的设计思路，Mesos-DNS自身不需要任务数据的持久化或者数据一致性的考虑，甚至Mesos-DNS自身的高可用还要依赖于Marathon框架的支持，Mesos-DNS要做的只是周期性的从Mesos Master上获取每个应用的信息并且响应DNS解析的请求。当有大量的DNS解析需求时我们只需增加Mesos-DNS的副本数即可。

在Mesos Marathon方案中进行服务发现的第二种方法是使用Marathon-lb，Marathon-lb实际是由Haproxy和一个可以通过Marathon REST API来生成和管理Haproxy配置的程序打包起来的容器。相比于Mesos-DNS，Marathon-lb只可以满足Marathon内的服务发现。

在Mesos Marathon方案中完成服务发现的第三种方法是haproxy-marathon-bridge，现在已经被Marathon-lb取代，其工作原理和Marathon-lb基本是一样的。

## **Mesos Marathon可用性管理**

Marathon可以很好的支持对单个容器健康性检查。我们可以通过HTTP（S）、TCP以及可执行命令等方式来探测应用的状态。而从应用可用性角度来说，除了对单个容器的检查Marathon还可以做到多副本的设置，这样就可以很方便的实现对无状态应用的负载均衡和高可用，同时Marathon还支持动态的扩缩容。最后Marathon提供了应用集群的可用性探测这样就可以更直观的了解每个应用的运行状态，应用的可用性探测目前是对HTTP(S)地址的套餐完成的。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              

## **Mesos Marathon IaaS** **资源管理和调度**

在Mesos Marathon方案中对IAAS的资源调度只需在提交任务时明确应用所需的内存、CPU资源需求即可，剩下的工作就交给Mesos和Marathon即可。但是我们依然可以通过制定一些约束条件来更好的实现应用编排和管理，主要是对主机名、Mesos Slave节点的标签设置响应的条件，比如我们要求每个Slave节点上只部署一个应用实例就可以要求Marathon在派发任务时每个节点只派发一个任务。又比如我们希望以机架为单位来部署应用实例就可以让Marathon在派发任务的时候按Mesos Slave的机架标签来对节点进行分组然后派发任务。当然这样做的前提是每个Mesos Slave节点上必须存在相应的标签，这些标签是在Mesos Slave启动的时候设置的。

## **Mesos Marathon持久化存储**

持久化卷的功能是在Marathon 1.0版中引入的为解决有状态应用数据存储问题的新特性，但现在还是beta状态，Marathon官网中特别对使用Marathon的持久化卷功能进行了风险提示。这意味着目前Marathon持久化的设计和功能实现完成度都还较低。Marathon目前可以支持两大类持久化卷，一类是Mesos Slave节点的本地存储，另一类是通过rexray项目来实现对外部存储的管理。但是rexray项目的局限在与很大程度上需要依赖IAAS和阵列的支持，在IAAS上包括AWS、GCE、OpenStack等，在阵列上包括ScaleIO、vmax等。

## **Mesos Marathon多租户管理**

Mesos Marathon本身并没有多租户的能力，但是借助Mesos的资源隔离能力，我们可以通过在Marathon中运行Marathon来变相的实现多租户的管理。也就是将Marathon划分成两层，第一层Marathon起到init进程的作用，它可以使用Mesos集群的所有资源，而第二层Marathon实际上是由多个Marathon实例组成，每个实例都和一个Mesos角色进行关联，通过Mesos的角色来决定某个Marathon实例能使用的资源额度，然后我们的应用就是由第二层Marathon来负责调度和管理，这个应用就只能使用分配到第二层Marathon的那块资源。这样就实现了Marathon的多租户。