# **第8章：Docker官方的PaaS框架—Swarm**

Swarm是由Docker官方提供的容器编排工具，它使用标准的Docker API和网络功能，因此很容器就能与已经使用Docker容器的环境中整合。

The original Docker Swarm product came in kit form and didn't have a core feature set built in. The Docker Swarm runtime itself ran as a container on each of the nodes, and you needed multiple additional technologies, like Consul or etcd for discovery and Nginx for load balancing. Your swarm would be running a mixture of infrastructure containers, alongside your own app containers.

Setting up an 'old' swarm wasn't straightforward either, because the discovery component had to be in place before you created the swarm, but then you would want the discovery running as part of the swarm, so you had a chicken-and-egg problem to resolve before you could do anything else (Jacob Blain Christen's article “[Toward a Production-Ready Docker Swarm Cluster with Consul](https://medium.com/on-docker/toward-a-production-ready-docker-swarm-cluster-with-consul-9ecd36533bb8#.h18lknb2q)” explains this well).

## Feature highlights
- **Cluster management integrated with Docker Engine:** Use the Docker Engine CLI to create a swarm of Docker Engines where you can deploy application services. You don’t need additional orchestration software to create or manage a swarm.
- **Decentralized design:** Instead of handling differentiation between node roles at deployment time, the Docker Engine handles any specialization at runtime. You can deploy both kinds of nodes, managers and workers, using the Docker Engine. This means you can build an entire swarm from a single disk image.
- **Declarative service model:** Docker Engine uses a declarative approach to let you define the desired state of the various services in your application stack. For example, you might describe an application comprised of a web front end service with message queueing services and a database backend.
- **Scaling:** For each service, you can declare the number of tasks you want to run. When you scale up or down, the swarm manager automatically adapts by adding or removing tasks to maintain the desired state.
- **Desired state reconciliation:** The swarm manager node constantly monitors the cluster state and reconciles any differences between the actual state your expressed desired state. For example, if you set up a service to run 10 replicas of a container, and a worker machine hosting two of those replicas crashes, the manager will create two new replicas to replace the replicas that crashed. The swarm manager assigns the new replicas to workers that are running and available.
- **Multi-host networking:** You can specify an overlay network for your services. The swarm manager automatically assigns addresses to the containers on the overlay network when it initializes or updates the application.
- **Service discovery:** Swarm manager nodes assign each service in the swarm a unique DNS name and load balances running containers. You can query every container running in the swarm through a DNS server embedded in the swarm.
- **Load balancing:** You can expose the ports for services to an external load balancer. Internally, the swarm lets you specify how to distribute service containers between nodes.
- **Secure by default:** Each node in the swarm enforces TLS mutual authentication and encryption to secure communications between itself and all other nodes. You have the option to use self-signed root certificates or certificates from a custom root CA.
- **Rolling updates:** At rollout time you can apply service updates to nodes incrementally. The swarm manager lets you control the delay between service deployment to different sets of nodes. If anything goes wrong, you can roll-back a task to a previous version of the service

## Swarm mode key concepts

#### Swarm

The cluster management and orchestration features embedded in the Docker Engine are built using**SwarmKit**. Engines participating in a cluster are running in **swarm mode**. You enable swarm mode for the Engine by either initializing a swarm or joining an existing swarm.
A **swarm** is a cluster of Docker Engines where you deploy [services](https://docs.docker.com/engine/swarm/key-concepts/#Services-and-tasks). The Docker Engine CLI includes the commands for swarm management, such as adding and removing nodes. The CLI also includes the commands you need to deploy services to the swarm and manage service orchestration.
When you run Docker Engine outside of swarm mode, you execute container commands. When you run the Engine in swarm mode, you orchestrate services.
#### Node
A **node** is an instance of the Docker Engine participating in the swarm.
To deploy your application to a swarm, you submit a service definition to a **manager node**. The manager node dispatches units of work called [tasks](https://docs.docker.com/engine/swarm/key-concepts/#Services-and-tasks) to worker nodes.
Manager nodes also perform the orchestration and cluster management functions required to maintain the desired state of the swarm. Manager nodes elect a single leader to conduct orchestration tasks.
**Worker nodes** receive and execute tasks dispatched from manager nodes. By default manager nodes are also worker nodes, but you can configure managers to be manager-only nodes. The agent notifies the manager node of the current state of its assigned tasks so the manager can maintain the desired state.
#### Services and tasks
A **service** is the definition of the tasks to execute on the worker nodes. It is the central structure of the swarm system and the primary root of user interaction with the swarm.
When you create a service, you specify which container image to use and which commands to execute inside running containers.
In the **replicated services** model, the swarm manager distributes a specific number of replica tasks among the nodes based upon the scale you set in the desired state.
For **global services**, the swarm runs one task for the service on every available node in the cluster.
A **task** carries a Docker container and the commands to run inside the container. It is the atomic scheduling unit of swarm. Manager nodes assign tasks to worker nodes according to the number of replicas set in the service scale. Once a task is assigned to a node, it cannot move to another node. It can only run on the assigned node or fail.
#### Load balancing

The swarm manager uses **ingress load balancing** to expose the services you want to make available externally to the swarm. The swarm manager can automatically assign the service a **PublishedPort** or you can configure a PublishedPort for the service in the 30000-32767 range.

External components, such as cloud load balancers, can access the service on the PublishedPort of any node in the cluster whether or not the node is currently running the task for the service. All nodes in the swarm route ingress connections to a running task instance.

Swarm mode has an internal DNS component that automatically assigns each service in the swarm a DNS entry. The swarm manager uses **internal load balancing** to distribute requests among services within the cluster based upon the DNS name of the service.

## How nodes work

Docker Engine 1.12 introduces swarm mode that enables you to create a cluster of one or more Docker Engines called a swarm. A swarm consists of one or more nodes: physical or virtual machines running Docker Engine 1.12 or later in swarm mode.

There are two types of nodes: [**managers**](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/#manager-nodes) and [**workers**](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/#worker-nodes).

![Swarm mode cluster](https://docs.docker.com/engine/swarm/images/swarm-diagram.png)

If you haven’t already, read through the [swarm mode overview](https://docs.docker.com/engine/swarm/) and [key concepts](https://docs.docker.com/engine/swarm/key-concepts/).

### Manager nodes

Manager nodes handle cluster management tasks:

- maintaining cluster state
- scheduling services
- serving swarm mode [HTTP API endpoints](https://docs.docker.com/engine/reference/api/)

Using a [Raft](https://raft.github.io/raft.pdf) implementation, the managers maintain a consistent internal state of the entire swarm and all the services running on it. For testing purposes it is OK to run a swarm with a single manager. If the manager in a single-manager swarm fails, your services will continue to run, but you will need to create a new cluster to recover.

To take advantage of swarm mode’s fault-tolerance features, Docker recommends you implement an odd number of nodes according to your organization’s high-availability requirements. When you have multiple managers you can recover from the failure of a manager node without downtime.

- A three-manager swarm tolerates a maximum loss of one manager.

- A five-manager swarm tolerates a maximum simultaneous loss of two manager nodes.

- An `N` manager cluster will tolerate the loss of at most `(N-1)/2` managers.

- Docker recommends a maximum of seven manager nodes for a swarm.

  > **Important Note**: Adding more managers does NOT mean increased scalability or higher performance. In general, the opposite is true.

### Worker nodes

Worker nodes are also instances of Docker Engine whose sole purpose is to execute containers. Worker nodes don’t participate in the Raft distributed state, make in scheduling decisions, or serve the swarm mode HTTP API.

You can create a swarm of one manager node, but you cannot have a worker node without at least one manager node. By default, all managers are also workers. In a single manager node cluster, you can run commands like `docker service create` and the scheduler will place all tasks on the local Engine.

To prevent the scheduler from placing tasks on a manager node in a multi-node swarm, set the availability for the manager node to `Drain`. The scheduler gracefully stops tasks on nodes in `Drain`mode and schedules the tasks on an `Active` node. The scheduler does not assign new tasks to nodes with `Drain` availability.

Refer to the [`docker node update`](https://docs.docker.com/engine/reference/commandline/node_update/) command line reference to see how to change node availability.

### Changing roles

You can promote a worker node to be a manager by running `docker node promote`. For example, you may want to promote a worker node when you take a manager node offline for maintenance. See [node promote](https://docs.docker.com/engine/reference/commandline/node_promote/).

You can also demote a manager node to a worker node. See [node demote](https://docs.docker.com/engine/reference/commandline/node_demote/).

## How services work

To deploy an application image when Docker Engine is in swarm mode, you create a service. Frequently a service will be the image for a microservice within the context of some larger application. Examples of services might include an HTTP server, a database, or any other type of executable program that you wish to run in a distributed environment.

When you create a service, you specify which container image to use and which commands to execute inside running containers. You also define options for the service including:

- the port where the swarm will make the service available outside the swarm
- an overlay network for the service to connect to other services in the swarm
- CPU and memory limits and reservations
- a rolling update policy
- the number of replicas of the image to run in the swarm

#### Services, tasks, and containers

When you deploy the service to the swarm, the swarm manager accepts your service definition as the desired state for the service. Then it schedules the service on nodes in the swarm as one or more replica tasks. The tasks run independently of each other on nodes in the swarm.

For example, imagine you want to load balance between three instances of an HTTP listener. The diagram below shows an HTTP listener service with three replicas. Each of the three instances of the listener is a task in the swarm.

![services diagram](https://docs.docker.com/engine/swarm/images/services-diagram.png)

A container is an isolated process. In the swarm mode model, each task invokes exactly one container. A task is analogous to a “slot” where the scheduler places a container. Once the container is live, the scheduler recognizes that the task is in a running state. If the container fails health checks or terminates, the task terminates.

#### Tasks and scheduling

A task is the atomic unit of scheduling within a swarm. When you declare a desired service state by creating or updating a service, the orchestrator realizes the desired state by scheduling tasks. For instance, the you define a service that instructs the orchestrator to keep three instances of a HTTP listener running at all times. The orchestrator responds by creating three tasks. Each task is a slot that the scheduler fills by spawning a container. The container is the instantiation of the task. If a HTTP listener task subsequently fails its health check or crashes, the orchestrator creates a new replica task that spawns a new container.

A task is a one-directional mechanism. It progresses monotonically through a series of states: assigned, prepared, running, etc. If the task fails the scheduler removes the task and its container and then creates a new task to replace it according to the desired state specified by the service.

The underlying logic of Docker swarm mode is a general purpose scheduler and orchestrator. The service and task abstractions themselves are unaware of the containers they implement. Hypothetically, you could implement other types of tasks such as virtual machine tasks or non-containerized process tasks. The scheduler and orchestrator are agnostic about they type of task. However, the current version of Docker only supports container tasks.

The diagram below shows how swarm mode accepts service create requests and schedules tasks to worker nodes.

![services flow](https://docs.docker.com/engine/swarm/images/service-lifecycle.png)

#### Replicated and global services

There are two types of service deployments, replicated and global.

For a replicated service, you specify the number of identical tasks you want to run. For example, you decide to deploy an HTTP service with three replicas, each serving the same content.

A global service is a service that runs one task on every node. There is no pre-specified number of tasks. Each time you add a node to the swarm, the orchestrator creates a task and the scheduler assigns the task to the new node. Good candidates for global services are monitoring agents, an anti-virus scanners or other types of containers that you want to run on every node in the swarm.

The diagram below shows a three-service replica in yellow and a global service in gray.

![global vs replicated services](https://docs.docker.com/engine/swarm/images/replicated-vs-global.png)

## Attach services to an overlay network

Docker Swarm Mode includes a Routing Mesh that enables multi-host networking. It allows containers on two different hosts to communicate as if they are on the same host. It does this by creating a Virtual Extensible LAN (VXLAN), designed for cloud-based networking.

The routing works in two different ways. Firstly, based on the public port exposed on the service. Any requests to the port will be distributed. Secondly, the service is given a Virtual IP address that is routable only inside the Docker Network. When requests are made to the IP address, they are distributed to the underlying containers. This Virtual IP is registered with the Embedded DNS server in Docker. When a DNS lookup is made based on the service name, the Virtual IP is returned.

Docker Engine swarm mode natively supports **overlay networks**, so you can enable container-to-container networks. When you use swarm mode, you don’t need an external key-value store. Features of swarm mode overlay networks include the following:

- You can attach multiple services to the same network.
- By default, **service discovery** assigns a virtual IP address (VIP) and DNS entry to each service in the swarm, making it available by its service name to containers on the same network.
- You can configure the service to use DNS round-robin instead of a VIP.

In order to use overlay networks in the swarm, you need to have the following ports open between the swarm nodes before you enable swarm mode:

- Port `7946` TCP/UDP for container network discovery.
- Port `4789` UDP for the container overlay network.

## some deep dive

Let us spend some time understanding the master and worker architecture in detail.

![img](http://collabnix.com/wp-content/uploads/2016/07/a5210ca9a287dd6755ec83114ca10748.png)

 

Every node in Swarm Mode has a role which can be categorized as  Manager and Worker. Manager node has responsibility to actually orchestrate the cluster, perform the health-check, running containers serving the API and so on. The worker node just execute the tasks which are actually containers. It can-not decide to schedule the containers on the different machine. It can-not change the desired state. The workers only takes work and report back the status. You can enable node promotion or demotion easily through one-liner command.

![img](http://collabnix.com/wp-content/uploads/2016/07/ffc07f20c6634cc9ab4147342367ace6.png)

Managers and Workers uses two different communication models. Managers have built-in RAFT system that allows them to share information for new leader election. At one time, only manager is actually performing the scaling and they use a leader follower model to figure out which one is supposed to be what. No External K-V store is required as built-in internal distributed state store is available.

Workers, on the other side, uses GOSSIP network protocol which is quite fast and consistent. Whenever any new container/tasks gets generated in the cluster, the gossip is going to broadcast it to all the other containers in a specific overlay network that this new container has started. Please remember that ONLY the containers which are running in the specific overlay network will be communicated and NOT globally. Gossip is optimized for heavy traffic.

![img](http://collabnix.com/wp-content/uploads/2016/07/e801da1b87d32a432f84535f15137b6c.png)

Let us go one level more deeper to understand how the underlying service is created and dispatched to the worker nodes. Before creating the service, let us first create a new overlay network called mynetwork.

![img](http://collabnix.com/wp-content/uploads/2016/07/e458642b57e615fc14a4f1a95ea66aed.png)

The inotify triggers the relevant output accordingly:

![img](http://collabnix.com/wp-content/uploads/2016/07/ce47e4257d8048abd58d143cd956b9f6.png)

Let’s create our first service:

##### $sudo docker-machine ssh test-master1 ‘sudo docker service create –name collabnix –replicas 3 \

#####    –network mynetwork dockercloud/hello-world

Once you run the above command, 3 replicas of services gets generated and distributed across the cluster nodes.

![img](http://collabnix.com/wp-content/uploads/2016/07/59f3c7cae8910e1189723dd387af8978.png)

[Under the hood] – Let’s understand what happens whenever a new service is created.

 

![img](http://collabnix.com/wp-content/uploads/2016/07/a3cd3d0451abe40207e3a49373094ca3.png)

Whenever we create overlay network through “docker network create -d overlay” command, it basically goes to manager. Manager is built up of multiple pipeline stages. One of them is Allocator. Allocator takes the network creation request and choose particular pre-defined sub network that is available. Allocation purely happen in the memory and hence it goes quick. Once network is created, it’s time to connect service to that network. Say, you start with service creation, orchestrator is involved and try to generate the requisite number of tasks which is nothing but containers in real world. But the tasks needs IP address, VXLAN ids as the overlay network needs that too. The allocation happens in the manager nodes. Once allocation gets completed, tasks are created and the state is preserved in the raft store. Once allocation is done, only then the scheduler will be able to move that particular task into the assigned state which is then dispatched to one of the worker node. Manager can also be worker. Every task goes through multiple stages – New, Allocated, Assigned etc. if the task has not been moved to allocator stage, it will not be assigned to worker nodes. With the help of network control plane(gossip protocol), multiple tasks distributed across the multiple worker node is taken care and managed effectively.

## **What’s new in Load-balancing feature under Docker 1.12.0?**

Load-balancing(LB) feature is not at all new for Docker. It was firstly introduced under Docker 1.10 release where Docker Engine implements an embedded DNS server for containers in user-defined networks.In particular, containers that are run with a network alias ( — net-alias) were resolved by this embedded DNS with the IP address of the container when the alias is used.

No doubt, DNS Round robin is extremely simple to implement and is an excellent mechanism to increment capacity in certain scenarios, provided that you take into account the default address selection bias but it possess certain limitations and issues like some applications cache the DNS host name to IP address mapping and  this causes applications to timeout when the mapping gets changed.Also, having non-zero DNS TTL value causes delay in DNS entries reflecting the latest detail. DNS based load balancing does not do proper load balancing based on the client implementation. To learn more about DNS RR which is sometimes called as poor man’s protocol, you can refer [here](https://medium.com/@lherrera/poor-mans-load-balancing-with-docker-2be014983e5#.pccsnmuz6).

- **Docker 1.12.0 comes with built-in Load Balancing feature now**.LB is designed as an integral part of Container Network Model (rightly called as CNM) and works on top of CNM constructs like network, endpoints and sandbox. Docker 1.12 comes with **VIP-based** Load-balancing.**VIP based services use Linux IPVS load balancing to route to the backend containers**


- **No more centralized Load-Balancer, it’s distributed and hence scalable.** LB is plumbed into individual container. Whenever container wants to talk to another service, LB is actually embedded into the container where it happens. LB is more powerful  now and just works out of the box.

[![Pci321](http://collabnix.com/wp-content/uploads/2016/08/Pci321-1024x715.png)](http://collabnix.com/wp-content/uploads/2016/08/Pci321.png)

- **New Docker 1.12.0 swarm mode uses IPVS**(kernel module called “ip_vs”) for load balancing. It’s a load balancing module integrated into the Linux kernel


- **Docker 1.12 introduces Routing Mesh for the first time.**With IPVS routing packets inside the kernel, swarm’s routing mesh delivers high performance container-aware load-balancing.Docker Swarm Mode includes a Routing Mesh that enables multi-host networking. It allows containers on two different hosts to communicate as if they are on the same host. It does this by creating a Virtual Extensible LAN (VXLAN), designed for cloud-based networking. we will talk more on Routing Mesh at the end of this post.

Whenever you create a new service in Swarm cluster, the service gets Virtual IP(VIP) address. Whenever you try to make a request to the particular VIP, the swarm Load-balancer will distribute that request to one of the container of that specified service. Actually the built-in service discovery resolves service name to Virtual-IP. Lastly, the service VIP to container IP load-balancing is achieved using IPVS. It is important to note here that **VIP is only useful within the cluster.** It has no meaning outside the cluster because it is a private non-routable IP.

