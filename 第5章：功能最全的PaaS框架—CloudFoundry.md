#  **第5章：功能最全的PaaS框架—CloudFoundry**

Cloud platforms let anyone deploy network applications or services and make them available to the world in a few minutes. When an application becomes more popular, the cloud easily scales it to handle more traffic, replacing with a few keystrokes the build-out and migration efforts that earlier took months.
Not all cloud platforms offer same set of facilities. Some have limited language and framework support, lack key application services or restrict deployment to a single cloud.Cloud Foundry has become the industry standard today. It’s **an open source platform** that you can run your applications on your own computing infrastructure, or deploy on an IaaS like AWS, vSphere, or OpenStack.

Cloud Foundry is an open source cloud computing PaaS. It was originally developed by **VMware** and now owned by **Pivotal Software**, a joint venture by EMC, VMware and General Electric. It was designed and developed by a **small team from Google led by Derek Collison** and was originally called **project B29**. Cloud Foundry was primarily written in Ruby and Go. The platform’s openness and extensibility prevent its users from being locked into a single framework, set of application services, or cloud. Cloud Foundry is ideal for anyone interested in removing the cost and complexity of configuring infrastructure for their applications. Developers can deploy their applications to Cloud Foundry using their existing tools and with zero modification to their code.

Cloud Foundry is an open source cloud computing PaaS. It was originally developed by **VMware** and now owned by **Pivotal Software**, a joint venture by EMC, VMware and General Electric. It was designed and developed by a **small team from Google led by Derek Collison** and was originally called **project B29**. Cloud Foundry was primarily written in Ruby and Go. The platform’s openness and extensibility prevent its users from being locked into a single framework, set of application services, or cloud. Cloud Foundry is ideal for anyone interested in removing the cost and complexity of configuring infrastructure for their applications. Developers can deploy their applications to Cloud Foundry using their existing tools and with zero modification to their code.
[![img](https://1.bp.blogspot.com/--NblGjMPcjM/Vp4agBxsW9I/AAAAAAAAGz8/SpbLftvn6Ps/s320/4.png)](http://1.bp.blogspot.com/--NblGjMPcjM/Vp4agBxsW9I/AAAAAAAAGz8/SpbLftvn6Ps/s1600/4.png)**\*“...we believe that the best cloud PaaS platform solution should be available on top of any cloud and not just locked into a specific one”***
**\*                                                                                                                                                **-Mike Soby in JAX magazine*
*                                                                                                                                                Special edition*
I will relate you a real story. A certain team created a new mobile app in weeks. But it took the IT team nine months to deploy it within their internal infrastructure which made two of the competitors to beat the market. This is not an unusual incident. Operations teams meet the massive complexity to configure, maintain and scale your applications in production. Network access rights, physical servers, OSes, VMs, App servers and web servers and many other systems come into play with applications. Even if the technical process is simple, human driven manual process adds tremendous latency that is potential for error. That's why traditional application deployment took months of downtime. Cloud Foundry **accelerates this application deployment**. It **fully automates not only initial infrastructure, but also ongoing life cycle events**. 
Developers can **build innovative applications** in weeks but then spend lot of time in configuring the infrastructure in middle ware stack. Cloud Foundry running in your public cloud can **remove the friction in how you build, deploy and operate applications. **
With Cloud Foundry, **developers can focus on the application code**. With a single command, they can deploy an application into the cloud within seconds. It **auto detects languages**, **selects run times** and **bind services **like databases to remove all that hard work for the development process. There's no need of configuring the environment. Thus huge efficiency is gained from i**nfrastructure automation, scaling and management**. Operators are now releaved from these manual tasks. These benefits come from operational capabilities built in Cloud Foundry. These features **reduce the complexity cost and time of scale**. **Log aggregation** lets you analyze application performance monitoring to solve problems of performance issues so that you can auto scale. Cloud Foundry **maximizes developer productivity in the mean time reducing operations complexity. **As a result, you can deploy new applications and new features to existing applications in less time than ever before. It **gives you the speed **to compete with internet startups. You can **focus on app innovation not on infrastructure management and scaling.** In the long term, you can **build your application and move it to any cloud**. That is what called **multi cloud portability**. Cloud Foundry is a **future proof** eco system. That means **you can always have the next new technology for your platform. **

**Ultimately, Cloud Foundry makes both application build and run in extremely scalable, fast and extensible way giving you the speeds you need to iterate in weeks for your company to be more innovative, competitive and profitable.**
Cloud Foundry is not only built to support popular frameworks today. It is also easy to add new frameworks. So you as a consumer of Cloud Foundry are kind of bullet proof yourselves for future cloud innovations that will come in weeks, months, years of ahead.
Cloud Foundry is completely open source under the apache 2 license and its code is available on Git Hub. We can trust that it really works.

### Choice of frameworks, application services, clouds

[![img](https://3.bp.blogspot.com/-Y6xqEUVF8ug/Vp4hAB9Au7I/AAAAAAAAG0M/XIAeu-rHXsc/s400/5.png)](http://3.bp.blogspot.com/-Y6xqEUVF8ug/Vp4hAB9Au7I/AAAAAAAAG0M/XIAeu-rHXsc/s1600/5.png)

So what do you have in Cloud Foundry? You have your own choice of frameworks. Currently, this supports a large space of frameworks such as Spring, nodeJS, Ruby on Rails, Python, Java and many more that you can choose for your development of choice. It also supports the ability to plug in custom run times.

You also have application service interfaces that basically allows you to plug in new services and support those services on top of Cloud Foundry. Most applications are not useful unless they have some data source or service or even some additional API that you want to plug on side. Currently Cloud Foundry supports Vfabric, Postgres database, Mysql database, RabbitMQ for messaging, Redis for key value store, mongoDB for unstructured document data and big data solutions.  What this looks like to you as an application developer is the ability to look up on your connection from environment you run in. 

Last but not the least the way how you deploy it. The multi cloud concept is important because when we choose our PaaS, we don't have to choose our cloud. Micro clouds can be run in our laptops for development. Then public clouds like Vmware can be run on public cloud environment. 

Since Cloud Foundry is open source, we can grab its code, run and host it. There are also vendors like pivotal that provides commercial offerings. Pivotal public cloud runs on top of Amazon. You can either take open source space Cloud Foundry and run it in your stack or you can purchase a free package product such as Pivotal Cloud Foundry. 

Micro cloud is the ability to spin up all of the components and run them in a single VM on your local machine. So if you want to contribute to Cloud Foundry, you can try it out in a single VM. If you have that local experience, it is exactly the same as it happens in public cloud. 

### A growing ecosystem

The users who are accustomed to Cloud Foundry has doubled every two months. Already tens of thousands of users are using it. Thousands of applications are deployed in it. There are multiple distributors and deployers. Community leads are also very important. Since Cloud Foundry is open source, it is looking for more and more partners to provide additional frameworks and solutions in Cloud Foundry. We call this “Community Leads Program”. AppFog which provides PHP and ActiveState which provides python are examples. If you want to join this program, go to [cloudfoundry.org](https://www.cloudfoundry.org/) and you will get the link to join the program. So this project has a strong open source community participation. There are hundreds of contributors from open source community. 

Source code of Cloud Foundry is available on github. This allows any developer to access, evaluate and modify code. It can be integrated with other frameworks as well. We can add application services and deploy to other infrastructure clouds. 

### Why we should deploy apps to Cloud Foundry? 

We can use Cloud Foundry to deploy, run and scale applications to meet our agile needs. It allows choice of frameworks and runtimes. It is not limited to a particular set of code. The competition is growing and Cloud Foundry is very active. Since it is completely open source, there are lots of innovations too. It is portable, scalable and flexible. As a developer we have the advantage that it is easy to deploy many instances. For that we don't have to do much. Once it is set up, we don't have to worry about how the VMs and containers are set up. That part is done for you as a service from cloud foundry PaaS. How ever I want to clarify a bit. If you are just deploying a single application, CF is not the right choice for you. Cloud Foundry is for massive applications which require scaling. In fact we can use Cloud Foundry as a service. 

### Multi tenancy support

Cloud Foundry supports multi tenancy too. It is an architecture in which a single instance of a software application serves multiple customers. Each customer is a tenant. Tenants are given ability to customize some parts of application such as UI or business rules, but they cannot customize application code. Each tenant's data is isolated and remains invisible to other tenants. Cloud Foundry is by default a **highly distributed multi tenant platform** that is usually deployed at a scale. For the novice Cloud Foundry developer, a full scale PaaS deployment is not possible and it is not an affordable choice. To address this issue, Cloud Foundry team released BoshLite which provides developers a method to deploy it locally in their machine.

### Why is an open cloud platform important?

Having something future proof, customizable or spacing , extensible or adapt to changes is important. We don't know what programming language will be introduced in the future. We don't know what's going to come from language perspective, hardware perspective or infrastructure perspective. I**t's really important that we are not locked in.** Don't be locked in. Because we don't know about future, we want a way of working and a platform that enables us to go with. 

### Moving to architecture ...

Clouds balance their processing loads over multiple machines, optimizing for efficiency and resilience against point failure. A Cloud Foundry installation accomplishes this at three levels: 

**- The BOSH system** creates and deploys virtual machines (VMs) on top of a physical computing infrastructure, and deploys and runs Cloud Foundry on top of this cloud. To configure the deployment, it follows a manifest document. 

**- The CF Cloud Controller **runs the applications and other processes on the cloud’s VMs, balancing demand and managing app life cycles. 

****

**- The Go Router** routes incoming traffic from the world to the VMs that are running the applications that the traffic demands, usually working with a customer-provided load balancer.

[![img](https://docs.cloudfoundry.org/concepts/images/cf_architecture_block.png)](https://docs.cloudfoundry.org/concepts/images/cf_architecture_block.png)

Cloud Foundry designates two types of VMs: 

                             -  **Component VMs **that consitute the platform’s infrastructure
                             - ** Application VMs** that host apps for the outside world

Within Cloud Foundry, the **Diego system** distributes the hosted app load over all the Application VMs, and keeps it running and balanced through demand surges, outages, or other changes. Diego accomplishes this through an auction algorithm. 

To meet demand, multiple Application VMs run duplicate instances of the same application. This means the apps must be portable. Cloud Foundry distributes application source code to VMs with everything needed to compile and run the apps locally.
This includes,
                    1) **OS stack** that the application runs on
                    2) **Buildpack** containing all languages, libraries, services etc. that the app uses.
Before sending an app to a VM, the **Cloud Controller **stages it for delivery by combining stack, buildpack, and source code into a **droplet** that the VM can unpack, compile, and run. For simple, standalone apps with no dynamic pointers, the droplet can contain a pre-compiled executable instead of source code, language, and libraries.

To **organize user access** to the cloud and to control resource use, a cloud operator defines orgs and spaces within an installation and assigns Roles to all users: admin, developer, manager, auditor, etc. The User Authentication and Authorization(**UAA**) server supports access control as **anOAuth2** **service**, and can store user information internally or connect to external user stores through LDAP or SAML. 

Cloud Foundry uses the git system on github to **version-control **source code, buildpacks, documentation, and other resources. Developers on the platform also use github for their own applications, custom configurations, etc. To store large binary files, such as droplets, Cloud Foundry maintains an internal **Blob Store**. To store and share temporary information, such as internal component states, CF uses the distributed value-store systems **Consul and etcd. **

Cloud Foundry components communicate with each other by posting messages internally via http and https protocol and by sending NATS messages to each other directly. 

As the cloud operates, the Cloud Controller VM, router VM, and all VMs running applications continuously generate logs and metrics. The **Loggregator system **corrals this information into structured, usable form. 

Typical applications depend on free or metered services such as databases or third-party APIs. To incorporate these into an application, a developer writes a **Service Broker**, an API that publishes to the Cloud Controller the ability to list service offerings, provision the service, and enable applications to make calls out to it.

Cloud Foundry components include a **self-service application execution engine**, an **automation engine** for application deployment and lifecycle management, and a s**criptable command line interface (CLI),** as well as integration with development tools to ease deployment processes. Cloud Foundry has an open architecture that includes a buildpack mechanism for adding frameworks, an application services interface, and a cloud provider interface. 

#### **Router**

The router routes incoming traffic to the appropriate component, usually the Cloud Controller or a running application on a DEA node.

#### **OAuth2 Server (UAA) and Login Server**

The OAuth2 server (the UAA) and Login Server work together to provide identity management.

#### 

#### **Cloud Controller**

The Cloud Controller is responsible for managing the life cycle of applications. When a developer pushes an application to Cloud Foundry, he is targeting the Cloud Controller. The Cloud Controller then stores the raw application bits, creates a record to track the application metadata, and directs a DEA node to stage and run the application. The Cloud Controller also maintains records of orgs, spaces, services, service instances, user roles, and more.

#### 

#### **Health Manager**

This has four core responsibilities: 

1. Monitor applications to determine their state (e.g. running, stopped, crashed, etc.), version, and number of instances. It updates the actual state of an application based on heartbeats and droplet.exited messages issued by the DEA running the application. 
2. Determine applications’ expected state, version, and number of instances. It obtains the desired state of an application from a dump of the Cloud Controller database. 
3. Reconcile the actual state of applications with their expected state. For instance, if fewer than expected instances are running, it will instruct the Cloud Controller to start the appropriate number of instances. 
4. Direct Cloud Controller to take action to correct any discrepancies in the state of applications. 

Health manager is essential to ensuring that apps running on Cloud Foundry remain available. It restarts applications whenever the DEA running an app shuts down for any reason, when Warden kills the app because it violated a quota, or when the application process exits with a non-zero exit code.

#### 

#### **Application Execution (DEA)**

The Droplet Execution Agent manages application instances, tracks started instances, and broadcasts state messages. Application instances live inside Warden containers. Containerization ensures that application instances run in isolation, get their fair share of resources, and are protected from noisy neighbors.

#### 

#### **Blob Store**

The blob store holds: 1. Application code 2. Buildpacks 3. Droplets

#### 

#### **Service Brokers**

Applications typically depend on services such as databases or third-party SaaS providers. When a developer provisions and binds a service to an application, the service broker for that service is responsible for providing the service instance.

#### 

#### **Message Bus (NATS)**

Cloud Foundry uses NATS, a lightweight publish-subscribe and distributed queueing messaging system, for internal communication between components.

#### 

#### **Metrics Collector and App Log Aggregator: Logging and Statistics**

The metrics collector gather metrics from the components. Operators can use this information to monitor an instance of Cloud Foundry. The application log aggregator streams application logs to developers.

Cloudfoundry: Open Source Platform as a Service**

A platform used for running applications and services. The purpose of cloudfoundry is to change the way apps and services are deployed and run by reducing the cycle time of development to deployment. Cloud Foundry directly takes benefits of cloud-based resources such that apps running on the platform can be unaware of underlying infrastructure.
It provides an arrangement to predictable and reliable running of cloud-native apps .

Cloud Foundry is an open platform, which allows for a choice of under‐lying infrastructure (eg. *OpenStack, AWS, Azure , vSphere*) , polyglot developer languages and frameworks,
and has a great range of application services. Also, Cloud Foundry is open sourced and controlled and governed by a multi-organization foundation ( nearly 60 companies ).
It is a powerful thing when different technology companies, industries, and business lines collaborate with such strong cohesion and momentum.

- Some of Supported Languages
  • Ruby
  • Python
  • PHP
  • NodeJS
  • PHP
  • Erlang
  • Java and JVM-based languages, such as Groovy
  • Perl
  • .NET
  • Haskell
- Some of Supported Frameworks
  • Rails
  • Sinatra
  • Rack
  • Java Spring
  • Catalyst
  • Dancer
- Some of Supported Services
  • PostgreSQL
  • MySQL
  • redis
  • neo4j
  • RabbitMQ
  • MongoDB
  • vblob

*Cloud Foundry Components*
Components of Cloud Foundry include a self-service application execution engine, an automation engine for application deployment and life cycle management, and a scriptable command line interface, as well as integration with development tools to ease deployment processes. It has an open architecture that includes a buildpack mechanism for adding frameworks, an application services interface, and a cloud provider interface. Refer diagram below :

![cf_architecture_block](http://www.tothenew.com/blog/wp-content/uploads/2016/07/cf_architecture_block.png)

*The Cloud Foundry Installation Process:*
Cloud Foundry can be installed as a single developer environment (via BOSH Lite) for experimentation, but is typically deployed into a larger infrastructure cloud via BOSH. Supported platforms include AWS, OpenStack, vSphere, vCloud Director and vCloud Air.
Details of Installation using Bosh Lite can be found at : [**BOSH Lite on github.**](https://github.com/cloudfoundry/bosh-lite/)

*The Application Life Cycle*

Typically, in most traditional scenarios, the application developer:
• Develops an application
• Deploys application services
• Deploys an application and connects (binds) it to application

*Application Design for the Cloud*
Applications written in supported application frameworks often run unmodified on Cloud Foundry, if the application design follows a few simple guidelines. Following these guidelines makes an application cloud-friendly, and facilitates deployment to Cloud Foundry and other cloud platforms.

- Avoid Writing to the Local File System as Local file system storage is short-lived and instances of the same application do not share a local file system.
- Ignore Unnecessary Files When Pushing
- Embrace Port Limitations
- Use Cookies Accessible across Applications
- Run Multiple Instances to Increase Availability
- Use Buildpacks

For those desiring to achieve velocity and establish a development-feedback cycle—and for those  challenged with responding to the technical driving forces relentlessly shaping today’s marketplaces—Cloud Foundry, as an established cloud-native platform, provides the most compelling way to enable the fundamental shift in the way we build and deploy software.



A buildpack provides framework and run time support for applications. Buildpacks examine user-provided artifacts to determine what dependencies to download and how to configure applications to communicate with different services.

When we push an application, Cloud Foundry automatically detects which buildpack is required and installs it on the Droplet Execution Agent where the application needs to run.

There are three elements of a buildpack. They are detect, compile and release. 

### Detect

A script to detect condition to run buildpack. Runs this detect script against deployed application. OK if exit 0. To get an idea, let's look into the detect script of ruby buildpack.

```
#!/bin/bash

if [ -f "$1/Gemfile" ]; then
SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )" echo "ruby $(cat $SCRIPT_DIR/../VERSION)" exit 0 else echo "no" exit 1 fi

```

If we analyse the script, what it does simply is doing a conditional check to see if there is a Gemfile. If so, ruby buildpack is used since the application is confirmed by the 'detect' script.

Detect can be written in ruby or bash.

There are some examples that detect itself is written in bash and then calls python script. cf-php buildpack is similar to that.

```
#!/bin/bash

BP=$(dirname $(dirname $0)) export PYTHONPATH=$BP/lib VERSION=`cat $BP/VERSION` python $BP/scripts/detect.py $1 $VERSION

```

### 

### Compile

This is the key script to setup language or framework runtime setup. 

What this does is, it downloads execution environment sources and then compiles. 

Because it takes time to do compile, many buildpacks download pre-compiled binaries.  

### Release

A script to return metadata required for execution in yaml format.

```
#!/usr/bin/env  ruby require 'pathname'  release = Pathname.new(ARGV.first).join("tmp/heroku-buildpack-release-step.yml") puts release.read if release.exist?

```

### Staging

Staging refers to building the droplet, which is the executable bundle of application. At this time, user deploys the application, process the application by buildpack description and then create the droplet. Let me describe this graphically. 

[![img](https://1.bp.blogspot.com/-MQX2QbYF41I/VvoKrrdxejI/AAAAAAAAG3o/SOflT3uoImo0DcL8nYlmaRm316ua38E5Q/s400/Selection_065.png)](https://1.bp.blogspot.com/-MQX2QbYF41I/VvoKrrdxejI/AAAAAAAAG3o/SOflT3uoImo0DcL8nYlmaRm316ua38E5Q/s1600/Selection_065.png)

\1. When user gives the command '*cf push*', user uploads the application to Cloud Controller. 

\2. Cloud controller sends the *'staging.start' *message to DEA to request staging activity. 

\3. DEA does staging and build the droplet.

\4. Once droplet is built, it returns back to cloud controller. 

[![img](https://4.bp.blogspot.com/-7O84gTQrtRo/VvoLztXl1xI/AAAAAAAAG3w/Qdc89fI4cdsIf-r2lR9kbtGIPcMIBSzFw/s400/Selection_066.png)](https://4.bp.blogspot.com/-7O84gTQrtRo/VvoLztXl1xI/AAAAAAAAG3w/Qdc89fI4cdsIf-r2lR9kbtGIPcMIBSzFw/s1600/Selection_066.png)

The above diagram explains the complete scenario.

You might be already wondering about what 'Diego' is. If so, this might be a very useful post for newbies in Cloud Foundry. So what 'Diego' actually is? How is it related to Cloud Foundry? How is it different from build-pack mechanism? Please be patient. You will soon find solutions to all your problems. Let's proceed then. 
What is DiegoIn a Cloud Foundry deployment without Diego, the Cloud Controller schedules and manages applications on the Droplet Execution Agent (DEA) nodes. Diego replaces the DEAs and the Health Manager, and assumes application scheduling and management responsibility from the Cloud Controller. Diego combines scheduler, runner and health manager. DEA, Health manager and Warden were rewritten in 'Go' , DEA + GO , hence it is called 'Diego'. 
Diego is a rewrite of the Cloud Foundry runtime. Why this was written again? The existing code is hard to maintain and add features. In case of the DEA and Health Manager, which Diego removes the need for, the code was too enmeshed with the Cloud Foundry code-base. So they wrote Cloud Foundry again in a different way which is now called 'Diego'.
At its core, Diego is a **distributed system** that **orchestrates** **containerized workloads**. I will describe each of these terms  separately.
**distributed system ? **If you look into running Diego installation, you will see a pile of VMs which you call 'cells'. They have containers running applications in them. There is also a handful of highly available VMs called 'Brain'. Then there are few machines called BBS which acts as a centralized data store that we use to coordinate information in a cluster. This is how Diego becomes a distributed system.
**orchestrates?**Diego's orchestration is responsible for two things. 1. Diego is a scheduler. So when you bring your droplet to Diego, Diego tries to optimally distributes it across running cells.
\2. Meanwhile, Diego implements health monitoring. So if your application crashes, Diego notices and restarts it. If your entire cell crashes, Diego will notice and save those applications.
**containerized workloads?**off-task is a unit of work that runs at most once inside a container. LRP or Long Running Process is complex. There can be 'N' long running instances that distributes across cells for high availability. There are LRPs and Tasks as well. This generic platform independent abstraction describes what Diego can do. Diego takes droplet which is the product of running 'cf push' and run buildpack based application on Diego. We are able to use same abstraction for docker based applications on Diego. 

Diego is responsible for the uptime of applications. It can stream output from the application processes and ensures the routing of requests to those applications. 

### How Diego is related with Docker

Docker is a container engine that makes it easy to back, ship and run software inside containers. Cloud Foundry has been employing containers since a long time to run applications. We  might want to run multiple instances of same app, that's where containers come into help. Cloud Foundry uses 'garden' technology engine to run these containers.
So why use Docker? As we know, Docker provides a convenient way for distributing containers. It defines something called 'image' which we can push to 'Docker Hub', so that the whole community can make use of it. 

****

**So if Docker is that good at distributed containers, why not combine the two: Cloud Foundry and Docker.  **Diego is an approach taken by combining these two technologies.

### Diego Architecture

[![img](https://docs.cloudfoundry.org/concepts/images/diego/diego-flow.png)](https://docs.cloudfoundry.org/concepts/images/diego/diego-flow.png)

1. The Cloud Controller passes requests to stage and run applications to the Cloud Controller Bridge (CC-Bridge).
2. The CC-Bridge translates staging and running requests into Tasks and Long Running Processes(LRPs), then submits these to the Bulletin Board System (BBS) through an RPC-style API over HTTP. BBS acts as a centralized data store that we use to coordinate information in a cluster. 
3. The BBS submits the Tasks and LRPs to the Auctioneer which is a part of the Diego Brain.
4. The Auctioneer distributes these Tasks and LRPs to Cells through an Auction.
5. Once the Auctioneer assigns a Task or LRP to a Cell, an in-process Executor creates a Garden container in the Cell. The Task or LRP runs in the container.
6. The BBS tracks desired LRPs, running LRP instances and in-flight Tasks for the Converger which is a part of the Diego Brain. The Converger periodically analyzes this information and corrects discrepancies to ensure consistency between ActualLRP and DesiredLRP counts.
7. Metron Agents which are part of the Cells, forward application logs, errors, and metrics to the Cloud Foundry Loggregator. 
8. Loggregator is the Cloud Foundry component responsible for logging, provides a stream of log output from your application and from Cloud Foundry system components that interact with your app during updates and execution. By default, Loggregator streams logs to your terminal. If you want to persist more than the limited amount of logging information that Loggregator can buffer, you can drain logs to a third-party log management service. Cloud Foundry gathers and stores logs in a best-effort manner. 

 

Implementation of containers is called 'Garden'.

 

make me a container

 

**put this in it**

 

**now go run this**

 

Diego has matured that it can run your containers natively without having to install anything. You have to install Diego CLI that makes pushing these images easily. Diego CLI is installed with cf-CLI. If you want to run your Docker containers using Diego native support, you have to do,

**cf push  -o .**

Otherwise you can deploy Diego to Bosh-Lite so that you can get the experience of deploying Docker images locally in your machine.  

**Key thing here is that Diego does not actually use Docker deamon to run an image. Rather than that, it just simulates Docker technology by using its own Garden tooling.  **

### Problems

Diego goes to internet, pulls out all bits of docker image and fires a container. Following problems came up with this approach initially.

                                                 **1. Unpredictable scaling**

**                                                 2. Private images**

**                                                 3. Performance**

#### 1. Unpredictable scaling

 '**cf scale -i  ** 

This tells Diego 'please start me this number of instances'. Then Diego goes to the internet, pulls latest version and completes your request. If in the meantime, the application provider decides to release a new version and push it to Docker Hub, then you will still end up running only two instances of that application because the previous version of that application is no longer supported. Thus you will have instances of both old version and new version. This is called unpredictable scaling.

#### 2. Performance

Another more obvious problem is performance. This approach is poorly sub optimal to each and every request. When scaling the number of instances, it goes and downloads the whole Docker image for each and every instance. We know that containers tend to be bigger. So this approach is not optimal.

#### 3. Private images

Diego lacks the private image support. Private images are available in Docker Hub, but they are protected by credentials. So if you want to support private images, then you need to deal with credentials. That's tricky. So either time you have to prompt user asking for credentials or else you have to steal credentials from database. This can be a problem if you are in an organization that decides to release your proprietary app in Docker Hub.

### Solution

Private Docker Registry

#### 1. Unpredictable scaling

#### 2. Performance

#### 3. Private images

From the beginning, Cloud Foundry was designed to run applications inside Linux containers. These restrict access to the host and other containers, preventing impact. Taking advantage of features in the Linux kernel, applications can run side-by-side on the same host without interfering with each other. This post explores the features of container technology and how this is implemented in [Cloud Foundry](http://docs.cloudfoundry.org/concepts/architecture/).

## Warden and Garden

Cloud Foundry’s container technology is provided by [Warden](https://github.com/cloudfoundry/warden), which was created by VMware’s Pieter Noorduis and others. Warden is a subtle combination of Ruby code, a core written in C, and shell scripts to configure the host and containers.

Warden provides a service for managing a collection of containers and defines a protocol for clients to send requests to and receive responses from the server. Each [DEA](http://docs.cloudfoundry.org/concepts/architecture/) host in a Cloud Foundry deployment runs the Warden service, which manages cgroups, namespaces, process life cycle, and provides telemetry about the state of the host and each container.  The Warden protocol is defined using Google protocol buffer definitions. Warden creates a root process, called “wshd” for Warden shell daemon, in each container. This root process is responsible for managing the container, launching application processes, and streaming the standard output and standard error back to the client.

[![warden structure](https://blog.pivotal.io/wp-content/uploads/2014/10/warden.png)](https://blog.pivotal.io/wp-content/uploads/2014/10/warden.png)Warden structure

More recently, Pivotal’s Alex Suraci and others have rewritten the Ruby portions of Warden in Go to produce Garden. Garden provides the container technology for the [Diego](https://blog.pivotal.io/cloud-foundry-pivotal/features/cf-summit-video-diego-reimagines-the-cloud-foundry-elastic-runtime) project (the future architecture for Cloud Foundry). Garden separates the server and protocol buffer handling (in a separate [Garden](https://github.com/cloudfoundry-incubator/garden) repository) from a [Garden Linux](https://github.com/cloudfoundry-incubator/garden-linux) *backend* which maps the protocol requests into Linux operating system primitives. The protocol is sufficiently platform agnostic for a Windows backend to be developed. Garden also supports a REST API closely modeled on the protocol buffer definition. The REST API is particularly useful during experimentation and testing.

[![garden structure](https://blog.pivotal.io/wp-content/uploads/2014/10/garden.png)](https://blog.pivotal.io/wp-content/uploads/2014/10/garden.png)Garden structure

## **Namespaces**

Applications necessarily consume named resources that appear on the host in various global namespaces. For example, an application might listen on a particular port, and this port is visible in the global network namespace of the host. To avoid applications clashing with each other and with other programs running in the host, the application is executed inside a collection of container-specific namespaces. For example, a network namespace isolates the IP addresses and ports used by an application from those in other network namespaces.

Linux has been adding support for namespaces for several years, starting with a namespace of file system mount points, and, most recently, support was added for a namespace of users. Michael Kerrisk has written an excellent series of [articles on Linux namespaces](http://lwn.net/) over on LWN.

Garden currently uses all the available Linux namespaces for containers except the user namespace, which is under discussion. Garden relies heavily on the mount namespace by replacing the host’s root file system (more below), using the Linux [pivot_root](http://linux.die.net/man/2/pivot_root) operation, with one specified by the user. It then unmounts the host’s root file system so the container cannot access it directly.

## Resource Control

Applications running inside namespaces are still free to consume anonymous operating system resources such as memory and CPU. They can also operate directly on devices. Linux provides [control groups](https://www.kernel.org/doc/Documentation/cgroups/cgroups.txt) to enable processes to be partitioned into hierarchical groups and subjected to constraints. Constraints are applied by *resource controllers* which participate in control groups and interface to the corresponding Linux kernel subsystems. For instance, the memory resource controller can limit the number of pages of real memory that may be consumed by a particular control group and can ensure processes are killed when the limit is about to be exceeded.

Garden uses five resource controllers—*cpuset* (CPUs and memory nodes) , *cpu* (CPU bandwidth),*cpuacct* (CPU accounting), *devices* (device access), and *memory* (memory usage)—and creates a control group for each container. The processes in the container are then subject to the constraints imposed by those resource controllers (except that cpuacct does not impose any constraints but accounts for CPU usage).

In addition to control groups, Garden uses a couple of other Linux features to impose limits on the processes in a container. Specifically, [setrlimit](http://linux.die.net/man/2/setrlimit) restricts the consumption of certain resources by processes in the container and [setquota](http://linux.die.net/man/8/setquota) restricts the consumption of certain other resources by users in the container.

## Networking Facilities

Since each container runs in a separate network namespace, Garden provides a way for network traffic to flow into and out of a container. It creates a pair of virtual ethernet devices, allocates an IP address for each device, and moves one of the devices into the container’s network namespace. Garden then sets up suitable IP routing table entries to ensure IP packets are routed correctly to and from the container. Finally, packet filtering rules create a firewall for the container. The firewall allows inbound and outbound traffic to be restricted.

[![container networking](https://blog.pivotal.io/wp-content/uploads/2014/10/networking.png)](https://blog.pivotal.io/wp-content/uploads/2014/10/networking.png)Container networking

## Root File System

Warden allows the user to configure a directory which is used as the root file system of all containers. Garden extends this behaviour so that each container can either use the configured directory as its root file system or can have a root file system built from a Docker image. Either way, a read-write layer is added to the root file system so that the container can update the root file system without affecting other containers.

## Garden API

The garden API, as defined in Google protocol buffers, has the following operations:

- Capacity – returns the memory and disk capacity of the host machine
- Create – creates a container and returns its *handle* (a string which identifies the container)
- Info – returns information about a specified container such as its IP address and a list of processes running in the container
- Run – spawns a process in the container and streams its output back to the client
- Attach – starts streaming the output of a specified process in a specified container back to the client
- List – lists all container handles
- LimitBandwidth, LimitCpu, LimitDisk, LimitMemory – adjusts the limits of a specified container for network bandwidth, CPU *shares*, disk usage, and memory usage, respectively
- NetIn – maps a port on the host machine to a port in the specified container
- NetOut – whitelists outbound network traffic from the specified container to a specified network and/or port
- StreamIn – copies data into a specified file in the specified container’s file system
- StreamOut – copies data out of a specified file in the specified container’s file system
- Ping – checks that the garden server is running
- Stop – terminates all processes in a specified container but leaves the container around (in*stopped* state)
- Destroy – destroys the specified container

## Other Applications

Cloud Foundry’s container technology has some other interesting applications. [BOSH Lite](https://github.com/cloudfoundry/bosh-lite) enables Cloud Foundry to run in a single virtual machine using a collection of containers instead of separate virtual machines for the various Cloud Foundry components. Another application is the [Concourse](http://concourse.ci/)continuous integration system which runs each CI job in a fresh container, isolated from other jobs. Supporting these additional use cases helps to keep Cloud Foundry’s container technology suited for general purpose.

## Conclusion

Cloud Foundry’s container technology, Garden, comprises a mostly platform agnostic front end and a platform-specific backend. The Linux backend relies heavily on standard Linux containers and operating system features such as namespaces, control groups, and various resource control and networking facilities to isolate containers from each other and limit their impact on the host virtual machine. Garden is designed to create containers, provide telemetry, and manage the container life cycle. Garden supports using a Docker image as the root file system of a container (as [demonstrated](http://thenewstack.io/docker-on-diego-cloud-foundrys-new-elastic-runtime/)at this year’s VMworld). A Windows backend is being investigated and other platforms are also feasible.