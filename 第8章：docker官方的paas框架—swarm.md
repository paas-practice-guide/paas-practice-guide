### **第8章：Docker官方的PaaS框架—Swarm**

Swarm是由Docker官方提供的容器编排工具，它使用标准的Docker API和网络功能，因此很容器就能与已经使用Docker容器的环境中整合。

### Swarm的关键要素和概念

| 要素及概念 | 说明 |
| --- | --- |
| Managers | Managers负责集群级分布式任务管理，负责调度Swarm集群的节点 |
| Workers | 运行Managers分配的容器
| Services | Swarm集群中指定的一组容器 |
| Tasks | the individual Docker containers running the image, plus commands, needed by a particular service　|
| Key-Value存储 |　etcd、Consul或者Zookeeper集群，用来存储swarm的状态数据并提供服务发现功能

