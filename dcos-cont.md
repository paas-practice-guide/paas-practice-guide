An operating system abstracts resources such as CPU, RAM, and networking and provides common services to applications. DC/OS is a distributed operating system that abstracts the resources of a cluster of machines and provides common services. These common services include running processes across a number of nodes, service discovery, and package management. This topic discusses the architecture of DC/OS and the interaction of its components.

To simplify the understanding of DC/OS, we will reuse the Linux terminology for kernel and user space. The kernel space is a protected area that is not accessible to users and involves low-level operations such as resource allocation, security, and process isolation. The user space is where the user applications and higher order services live, for example the GUI of your OS.

The DC/OS kernel space is comprised of Mesos masters and Mesos agents. The user space includes System Components such as Mesos-DNS, Distributed DNS Proxy, and services such as Marathon or Spark. The user space also includes processes that are managed by the services, for example a Marathon application.

![dcos-architecture-100000ft-4](https://docs.mesosphere.com/wp-content/uploads/2016/06/dcos-architecture-100000ft-4.png)

Before we dive into the details of the interaction between different DC/OS components, let’s define the terminology used.

- **Master:** aggregates resource offers from all agent nodes and provides them to registered frameworks.
- **Scheduler:** the scheduler component of a service, for example the Marathon scheduler.
- **User:** also known as a client, is an application either internal or external to the cluster that kicks off a process, for example a human user that submits a Marathon app spec.
- **Agent:** runs a discrete Mesos task on behalf of a framework. It is an agent instance registered with the Mesos master. The synonym of agent node is worker or slave node. You can have private or public agent nodes.
- **Executor:** launched on agent nodes to run tasks for a service.
- **Task:** a unit of work scheduled by a Mesos framework and executed on a Mesos agent.
- **Process:** a logical collection of tasks initiated by a client, for example a Marathon app or a Metronome job.

#### Kernel space

In DC/OS, the kernel space manages resource allocation and two-level scheduling across the cluster. The two types of processes in the kernel space are Mesos masters and agents:

- **Mesos masters** The `mesos-master` process orchestrates tasks that are run on Mesos agents. The Mesos Master process receives resource reports from Mesos agents and distributes those resources to registered DC/OS services, such as Marathon or Spark. When a leading Mesos master fails due to a crash or goes offline for an upgrade, a standby Mesos master automatically becomes the leader without disrupting running services. Zookeeper performs leader election.
- **Mesos agents**: Mesos agent nodes run discrete Mesos tasks on behalf of a framework. Private agent nodes run the deployed apps and services through a non-routable network. Public agent nodes run DC/OS apps and services in a publicly accessible network. The `mesos-slave` process on a Mesos agent manages its local resources (CPU cores, RAM, etc.) and registers these resources with the Mesos masters. It also accepts schedule requests from the Mesos master and invokes an Executor to launch a Task via [containerizers](http://mesos.apache.org/documentation/latest/containerizer/):The Mesos containerizer provides lightweight containerization and resource isolation of executors using Linux-specific functionality such as cgroups and namespaces.The Docker containerizer provides support for launching tasks that contain Docker images.

#### User space

The DC/OS user space spans systemd services and user services such as Chronos or Kafka.

-Systemd services are installed and are running by default in the DC/OS cluster and include the following:
-The Admin Router is an open source NGINX configuration that provides central authentication and proxy to DC/OS services.
-Exhibitor automatically configures ZooKeeper during installation and provides a usable Web UI to ZooKeeper.
-Mesos-DNS provides service discovery, allowing apps and services to find each other by using the domain name system (DNS).
-Minuteman is the internal layer 4 load balancer.
-Distributed DNS Proxy is the internal DNS dispatcher.
-DC/OS Marathon, the native Marathon instance that is the ‘init system’ for DC/OS, starts and monitors DC/OS services.
-ZooKeeper, a high-performance coordination service that manages the DC/OS services.
-User services
-A user service in DC/OS consists of a scheduler (responsible for scheduling tasks on behalf of a user) and an executor (running tasks on agents).
-User-level applications, for example an NGINX webserver launched through Marathon.

### Distributed process management

This section describes the management of processes in a DC/OS cluster, from the resource allocation to the execution of a process.

At a high level, this interaction takes place between the DC/OS components when a user launches a process. Communication occurs between the different layers, such as the user interacting with the scheduler, and within a layer, for example, a master communicating with agents.

![dcos-architecture-distributed-process-management-concept-4](https://docs.mesosphere.com/wp-content/uploads/2016/06/dcos-architecture-distributed-process-management-concept-4-1200x755.png)

Here is an example, using the Marathon service and a user launching a container based on a Docker image:

![dcos-architecture-distributed-process-management-example-5](https://docs.mesosphere.com/wp-content/uploads/2016/06/dcos-architecture-distributed-process-management-example-5-1200x687.png)

### Stateful Storage Support

DC/OS gives your services multiple persistent and ephemeral storage options.

External persistent volumes, the bread and butter of block storage, are available on many platforms. These are easy to use and reason about because they work just like legacy server disks, however, by design, they compromise speed for elasticity and replication.

Distributed file systems are a staple of cloud native applications, but they tend to require thinking about storage in new ways and are almost always slower due to network-based interaction.

Local ephemeral storage is the Mesos default for allocating temporary disk space to a service. This is enough for many stateless or semi-stateless 12-factor and cloud native applications, but may not be good enough for stateful services.

Local persistent volumes bridge the gap and provide fast, persistent storage. If your service is replicating data already or your drives are RAID and backed up to nearline or tape drive, local volumes might give you enough fault tolerance without the speed tax.

### IP per Container with Extensible Virtual Networks (SDN)

DC/OS comes built-in with support Virtual Networks leveraging Container Network Interface(CNI) standard. By default, there is one Virtual Network named ‘dos’ is created and any container that attaches to a Virtual Network, receives its own dedicated IP. This allows users to run workloads that are not friendly to dynamically assigned ports and would rather bind the existing ports that is in their existing app configuration. Now, with support for dedicated IP/Container, workloads are free to bind to any port as every container has access to the entire available port range.

### Network Isolation of Virtual Network Subnets

DC/OS now supports the creation of multiple virtual networks at install time and associate non-overlapping subnets with each of the virtual networks. Further, DC/OS users can program Network Isolation rules across DC/OS Agent nodes to ensure that traffic across Virtual Network subnets is isolated.