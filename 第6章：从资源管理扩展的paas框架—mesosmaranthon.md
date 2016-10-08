# **第6章：从资源管理扩展的PaaS框架—Mesos&Maranthon**

Mesos started as a research project at UC Berkeley in early 2009 by Benjamin Hindman, Andy Konwinski, Matei Zaharia, Ali Ghodsi, Anthony D. Joseph, Randy Katz, Scott Shenker, and Ion Stoica. Benjamin then brought Mesos to Twitter where it now runs their datacenters, and later became Chief Architect at Mesosphere which builds the Mesosphere Datacenter Operating System (DCOS).

## A Basic Overview of Apache Mesos

Apache Mesos is an open source cluster manager that simplifies running applications on a scalable cluster of servers, and is the heart of the Mesosphere system. Mesos leverages features of the modern kernel—"cgroups" in Linux, "zones" in Solaris—to provide isolation for CPU, memory, I/O, file system, rack locality, etc. The big idea is to make a large collection of heterogeneous resources. Mesos introduces a distributed two-level scheduling mechanism called resource offers. Mesos decides how many resources to offer each framework, while frameworks decide which resources to accept and which computations to run on them. It is a thin resource sharing layer that enables fine-grained sharing across diverse cluster computing frameworks, by giving frameworks a common interface for accessing cluster resources.The idea is to deploy multiple distributed systems to a shared pool of nodes in order to increase resource utilization. A lot of modern workloads and frameworks can run on Mesos, including Hadoop, Memecached, Ruby on Rails, Storm, JBoss Data Grid, MPI, Spark and Node.js, as well as various web servers, databases and application servers.

Mesos offers many of the features that you would expect from a cluster manager, such as:

- Scalability to over 10,000 nodes
- Resource isolation for tasks through Linux Containers
- Efficient CPU and memory-aware resource scheduling
- Highly-available master through Apache ZooKeeper
- Web UI for monitoring cluster state

### Mesos Architecture

Mesos has an architecture that is composed of master and slave daemons, and frameworks. Here is a quick breakdown of these components, and some relevant terms:

- **Master daemon**: runs on a master node and manages slave daemons
- **Slave daemon**: runs on a master node and runs tasks that belong to frameworks
- **Framework**: also known as a Mesos application, is composed of a *scheduler*, which registers with the master to receive resource *offers*, and one or more *executors*, which launches *tasks* on slaves. Examples of Mesos frameworks include Marathon, Chronos, and Hadoop
- **Offer**: a list of a slave node's available CPU and memory resources. All slave nodes send offers to the master, and the master provides offers to registered frameworks
- **Task**: a unit of work that is scheduled by a framework, and is executed on a slave node. A task can be anything from a bash command or script, to an SQL query, to a Hadoop job
- **Apache ZooKeeper**: software that is used to coordinate the master nodes

![Mesos Architecture](https://assets.digitalocean.com/articles/mesosphere/mesos_architecture.png)

**Note:** "ZK" represents ZooKeeper in this diagram.

This architecture allows Mesos to share the cluster's resources amongst applications with a high level of granularity. The amount of resources offered to a particular framework is based on the policy set on the master, and the framework scheduler decides which of the offers to use. Once the framework scheduler decides which offers it wants to use, it tells Mesos which tasks should be executed, and Mesos launches the tasks on the appropriate slaves. After tasks are completed, and the consumed resources are freed, the resource offer cycle repeats so more tasks can be scheduled.

This bring us to most important concepts in Mesos. They are:

- **resource offers**
- **resource isolation**
- **two-tier scheduling**

**Resource offer**. Each Mesos slave advertised it’s resources to the master. For example, A Mesos slave advertises to the leading Mesos master that it has 8 CPUs, 16 GB memory, and 64 GB disk available. The Mesos allocation module decides that the master should advertise the entire resource offer to Framework A’s scheduler. Framework A’s scheduler only accepts half of the resources offered to it, leaving 4 CPUs, 8 GB memory, and 32 GB disk available for other applications.
The Mesos allocation module decides that the master should advertise all of the remaining (unallocated) resources in the offer to Framework B’s scheduler. One of the examples of frameworks could be Apache Spark and Marathon.
**Mesos Resource Offer**
[![mesos (1)](http://eugenedvorkin.com/wp-content/uploads/2015/07/mesos-1.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/mesos-1.png)

**Resource Isolation.** Second concept is resource isolation. How Mesos are able to run completly different software on one node? Mesos has built-in support for isolating processes in containers using Linux control groups (cgroups) and Docker containers, allowing multiple applications to run alongside each other on a single machine. Mesos also support custom containerizers as well.
[![isolation](http://eugenedvorkin.com/wp-content/uploads/2015/07/isolation.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/isolation.png)

Last concept is **Resource allocation**. The Mesos master gets information on the available resources from the Mesos slaves,and based on resource policies, the Mesos master offers these resources to different frameworks. Mesos is using Weighted Dominant Resource Fairness algorithm to decide which framework which resources get offered. For details, please check “[Dominant Resource Fairness: Fair Allocation of Multiple Resource Types](http://static.usenix.org/events/nsdi11/tech/full_papers/Ghodsi.pdf)” Why it is weigthed? Because you can provide weight to influence allocation decision, for example: –weights prod:30, QA:20, dev:10. This way we allocated 30% of resources to prod and 20% to QA.

[![drf](http://eugenedvorkin.com/wp-content/uploads/2015/07/drf.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/drf.png)

Mesos allow to run many applications without any changes. But one thing it’s running spark and some other Big Data tools that already distributed and aware of Mesos, and another is our web services. So the question is **How do I run my microservices (SOA) on Mesos?** And the answer is simple. You don’t have to change your application. All we need is to use meta-framework like Marathon. **Marathon**, provides API for starting, stopping and scaling services, is a framework used for running long-running services on Mesos. 

[![marathon](http://eugenedvorkin.com/wp-content/uploads/2015/07/marathon.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/marathon.png)
These services have high availability requirements, which means that Marathon should be able to start the service instance
on other machines in case of a failure and should be able to scale elastically. While Mesos provide out of the box isolation level with cgroup, I would suggest wrapping microservices in dockers container.

**Chronos scheduler**, a cron replacement for automatically starting and stopping services (and handling failures) that runs on top of Mesos. 
Typical jobs include backups, Extract-Transform-Load (ETL) jobs, running other frameworks, and so on.
Chronos allows you to run shell scripts and also supports dependencies and retries.

![img](https://opensource.com/sites/default/files/images/business-uploads/mesos3.png)

*Workloads in Chronos and Marathon (source)*

There are a number of software projects built on top of Apache Mesos:

**Long Running Services**

- **Aurora** is a service scheduler that runs on top of Mesos, enabling you to run long-running services that take advantage of Mesos' scalability, fault-tolerance, and resource isolation.
- **Marathon** is a private PaaS built on Mesos. It automatically handles hardware or software failures and ensures that an app is "always on."
- **Singularity** is a scheduler (HTTP API and web interface) for running Mesos tasks: long running processes, one-off tasks, and scheduled jobs.
- **SSSP** is a simple web application that provides a white-label "Megaupload" for storing and sharing files in S3.

**Big Data Processing**

- **Cray Chapel** is a productive parallel programming language. The Chapel Mesos scheduler lets you run Chapel programs on Mesos.
- **Dpark** is a Python clone of Spark, a MapReduce-like framework written in Python, running on Mesos.
- **Exelixi** is a distributed framework for running genetic algorithms at scale.
- **Hadoop :** Running Hadoop on Mesos distributes MapReduce jobs efficiently across an entire cluster.
- **Hama** is a distributed computing framework based on Bulk Synchronous Parallel computing techniques for massive scientific computations e.g., matrix, graph and network algorithms.
- **MPI** is a message-passing system designed to function on a wide variety of parallel computers.
- **Spark** is a fast and general-purpose cluster computing system which makes parallel jobs easy to write.
- **Storm** is a distributed realtime computation system. Storm makes it easy to reliably process unbounded streams of data, doing for realtime processing what Hadoop did for batch processing.

**Batch Scheduling**

- **Chronos** is a distributed job scheduler that supports complex job topologies. It can be used as a more fault-tolerant replacement for cron.
- **Jenkins** is a continuous integration server. The mesos-jenkins plugin allows it to dynamically launch workers on a Mesos cluster depending on the workload.
- **JobServer** is a distributed job scheduler and processor which allows developers to build custom batch processing Tasklets using point and click web UI.
- **Torque** is a distributed resource manager providing control over batch jobs and distributed compute nodes.

**Data Storage**

- **Cassandra** is a highly available distributed database. Linear scalability and proven fault-tolerance on commodity hardware or cloud infrastructure make it the perfect platform for mission-critical data.
- **ElasticSearch** is a distributed search engine. Mesos makes it easy to run and scale.
- **Hypertable** is a high performance, scalable, distributed storage and processing system for structured and unstructured data.

What type of application are good candidate for Mesos?

[![use-mesos](http://eugenedvorkin.com/wp-content/uploads/2015/07/use-mesos.png)](http://eugenedvorkin.com/wp-content/uploads/2015/07/use-mesos.png)

Stateful, or monolithic, applications that need to persist data to disk **are ** **not **good candidates for running on Mesos

## A Basic Overview of Marathon

Marathon is a framework for Mesos that is designed to launch long-running applications, and, in Mesosphere, serves as a replacement for a traditional `init` system. It has many features that simplify running applications in a clustered environment, such as high-availability, node constraints, application health checks, an API for scriptability and service discovery, and an easy to use web user interface. It adds its scaling and self-healing capabilities to the Mesosphere feature set.

Marathon can be used to start other Mesos frameworks, and it can also launch any process that can be started in the regular shell. As it is designed for long-running applications, it will ensure that applications it has launched will continue running, even if the slave node(s) they are running on fails.

### Features

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


### Marathon orchestrates both apps and frameworks

The graphic below shows how Marathon runs on [Apache Mesos](https://mesos.apache.org/) acting as the orchestrator for other applications and services.

Marathon is the first framework to be launched, running directly alongside Mesos. This means the Marathon scheduler processes are started directly using `init`, `upstart`, or a similar tool.

Marathon is a powerful way to run other Mesos frameworks: in this case, [Chronos](https://github.com/mesos/chronos). Marathon launches two instances of the Chronos scheduler using the Docker image `mesosphere/chronos`. The Chronos instances appear in orange on the top row.

If either of the two Chronos containers fails for any reason, then Marathon will restart them on another slave. This approach ensures that two Chronos processes are always running.

Since Chronos itself is a framework and receives resource offers, it can start tasks on Mesos. In the use case below, Chronos is running two scheduled jobs, shown in blue. One dumps a production MySQL database to S3, while another sends an email newsletter to all customers via Rake.

Meanwhile, Marathon also runs the other application containers - either Docker or Mesos - that make up our website: JBoss servers, Jetty, Sinatra, Rails, and so on.

We have shown that Marathon is responsible for running other frameworks, helps them maintain 100% uptime, and coexists with them creating workloads in Mesos.

![img](https://mesosphere.github.io/marathon/img/architecture.png)

### Scaling and fault recovery

The next three images illustrate scaling and container placement.

Below we see Marathon running three applications, each scaled to a different number of containers: Search (1), Jetty (3), and Rails (5).

![img](https://mesosphere.github.io/marathon/img/marathon1.png)

As the website gains traction, we decide to scale out the Search service and our Rails-based application.

We use the Marathon REST API call to to add more instances. Marathon will take care of placing the new containers on machines with spare capacity, honoring the constraints we previously set. We can see the containers are dynamically placed:

![img](https://mesosphere.github.io/marathon/img/marathon2.png)

Finally, imagine that one of the datacenter workers trips over a power cord and a server is unplugged. No problem for Marathon: it moves the affected Search and Rails containers to a node that has spare capacity. Marathon has maintained our uptime in the face of machine failure.

![img](https://mesosphere.github.io/marathon/img/marathon3.png)






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