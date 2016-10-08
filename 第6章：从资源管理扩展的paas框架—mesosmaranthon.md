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
