# **第8章：Docker官方的PaaS框架—Swarm**

Swarm是由Docker官方提供的容器编排工具，它使用标准的Docker API和网络功能，因此很容器就能与已经使用Docker容器的环境中整合。在Docker1.12版以后Swarm的集群管理功能被集成如Docker引擎中，这样只要安装了Docker就是通过Swarm模式来进行容器集群管理。

## **运行时环境**

作为容器的编排和调度系统，Swarm也不限制运行时，只要是Docker容器都可以运行在Swarm集群中。在一个Swarm集群中有管理（Manager）节点和工作（Worker）节点，在Swarm中一次完整的容器调度叫任务（task）管理节点把任务分配给工作节点，在任务中包含了Docker容器和待执行的容器命令，目前Swarm任务是无法迁移的。

## **服务发现与路由**

Docker Swarm模式除了把集群管理功能集成入Docker引擎之外，Swarm模式引入了一个Service的概念，对于Swarm Service对内会提供一个DNS，每个Service都是一个DNS入口，集群内的负载均衡就通过DNS完成。同时Swarm Service会在每个节点上映射一个端口，默认从30000-32767自动分配，集群外可以通过访问集群内的任意节点的Service映射端口来访问应用，例如云平台的负载均衡器所代理的端口就要配置为Service映射的端口。

## **可用性管理**

Docker Swarm的Service可以进行可用性管理。相比于Kubernetes的Service，Swarm的Service包含了RC、DaemonSet的功能，也就是Swarm Service除了可以为一组容器进行负载均衡、服务发现以外，还可以通过replicated service来实现容器副本的管理，当因为工作节点出现异常时，管理节点会在正常的工作节点上自动启动相应的副本，目前Swarm还提供容器健康检查的能力，在启动Service的时候我们可以指定一个容器中的命令来确定容器运行是否正常，同时我们还可以指定指定健康检查的间隔和重试次数。Service除了有replicated模式，我们还可以通过global service来实现在集群中每个节点上启动一个应用容器实例，类似Kubernetes的DaemonSet。

## **编排**

在1.13版本以后的Docker Swarm中增加了对Docker compose的支持，这样我们就可以通过Docker compose来操作Docker Swarm集群来实现应用的快速部署。

## **IaaS** **资源管理和调度**

在Swarm集群中有管理节点和工作节点，他们可以是物理机也可以是虚拟机，只要在每个节点上安装1.12及以上版本的Docker引擎即可，Swarm的管理节点在派发任务时只会考虑节点的负载情况，管理节点总是向资源使用率最低的节点上派发任务，目前还没有亲和性和反亲和性的设置，但是Swarm的调度支持一些约束条件，包括节点的ID、节点的主机名、节点的角色、节点的标签以及Docker引擎的标签。我们可以通过制定这些约束条件来干预Swarm的调度过程。

作为Docker容器的编排系统，Swarm肯定可以在启动Service的时候指定给任务分配的内存、CPU额度，这样我们就可以更细粒度的管理工作节点的资源使用情况。

## **持久化存储**

Docker swarm可以通过两种方式来实现容器数据的持久化，一种是bind模式，直接使用工作节点的本地存储，一种是mount模式，在任务中通过Docker volume插件来实现数据的持久化，Docker官方推荐使用mount模式，也就是通过Docker volume来持久化数据，一是直接挂载工作节点的目录会存在一定的安全隐患；二是如果使用bind模式就要求在每个工作节点上提前分配相应的路径，因为swarm的任务调度是随机的；三是工作节点上的数据不具备迁移性，一旦某个任务为重新调度，历史数据并不会随着工作节点的变化而迁移。
