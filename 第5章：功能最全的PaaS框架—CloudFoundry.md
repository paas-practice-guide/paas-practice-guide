#  **第5章：功能最全的PaaS框架—CloudFoundry**

## 系统架构

CloudFoundry是一个开源的平台即服务平台，Cloudfoundry可以完成应用的构建、部署、测试以及弹性伸缩，Cloudfoundry是目前经过一系列的私有和公有云部署实施验证的开源项目。

Cloudfoundry是由应用部署、应用执行、应用生命周期管理、可编程客户端等组建组成，同时Cloudfoundry提供了buildpack机制以及服务接口来保证Cloudfoundry的开放性，其中buildpack可以用来支持丰富的运行时和框架，服务接口用来对接后端服务，Cloudfoundry一方面提供了一些默认的服务接口，例如mysql、redis等，还可以让最终用户用户servicebroker及用户自带服务等形式来扩展应用中所需的后端服务，这些在后续章节中会展开介绍。

![Cloud Foundry Architecture](http://docs.cloudfoundry.org/concepts/images/cf_architecture_block.png)

## CloudFoundry运行时支持-- buildpack

Cloudfoundry对运行时和各种框架的支持是通过将其打包成buildpack来实现应用程序对运行时和框架的支持，当我们push一个制品（aitfisres）到cloudfoundry的时候，Cloudfoundry会自动判断所需的buildpack，当然这里最好是显示的指定buildpack以防cloudfoundry判断失误，Cloudfoundry通过buildpack监测、分析用户提供的所谓制品（aitfisres）（这里的制品大多数时间都是代码、配置以及依赖约束）来判断所需的运行时，从何处下载相关依赖（例如执行pip intall -r requirement.txt来安装python的依赖，执行npm install 来安装nodejs的依赖），如何去配置应用程序（例如cloudfoundry指定给应用的端口，通过环境变量来配置绑定到应用的后端服务），在上去工作完成后buildpack和应用打包安装到Diego cell或者DEA中运行。

When you push an application, Cloud Foundry automatically detects which buildpack is required and installs it on the Diego cell or Droplet Execution Agent (DEA) where the application needs to run.

Cloud Foundry includes a set of system buildpacks for common languages and frameworks. This table lists the system buildpacks.

Cloudfoundry官方目前支持的通用语言和框架buildpack，具体列表如下：

| Name                                     | Supported Languages, Frameworks, and Technologies | GitHub Repo                              |
| ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| [Java](http://docs.cloudfoundry.org/buildpacks/java/index.html) | Grails, Play, Spring, or any other JVM-based language or framework | https://github.com/cloudfoundry/java-buildpack |
| [Ruby](http://docs.cloudfoundry.org/buildpacks/ruby/index.html) | Ruby, JRuby, Rack, Rails, or Sinatra     | https://github.com/cloudfoundry/ruby-buildpack |
| [Node.js](http://docs.cloudfoundry.org/buildpacks/node/index.html) | Node or JavaScript                       | https://github.com/cloudfoundry/nodejs-buildpack |
| [Binary](http://docs.cloudfoundry.org/buildpacks/binary/index.html) | *n/a*                                    | https://github.com/cloudfoundry/binary-buildpack |
| [Go](http://docs.cloudfoundry.org/buildpacks/go/index.html) | Go                                       | https://github.com/cloudfoundry/go-buildpack |
| [PHP](http://docs.cloudfoundry.org/buildpacks/php/index.html) | Cake, Symfony, Zend, Nginx, or HTTPD     | https://github.com/cloudfoundry/php-buildpack |
| [Python](http://docs.cloudfoundry.org/buildpacks/python/index.html) | Django or Flask                          | https://github.com/cloudfoundry/python-buildpack |
| [Staticfile](http://docs.cloudfoundry.org/buildpacks/staticfile/index.html) | HTML, CSS, JavaScript, or Nginx          | https://github.com/cloudfoundry/staticfile-buildpack |

以上buildpack默认包含在cloudfoundry的安装中，可以做到开箱即用，但是如果程序所需的运行时或框架不包含在官方支持的列表里面的话可以cloudfoundry还有一些由社区贡献的buildpack（https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks）可以使用，或者Heroku的buildpack也可以在cloudfoundry上使用（https://devcenter.heroku.com/articles/third-party-buildpacks），如果以上都没有满足要求的buildpack的话，我们可以定制自己的buildpack，比如从官方或者社区fork一份buildpack代码，然后在此基础上添加、修改我们需要的内容。


## CloudFoundry后端服务支持

在Cloudfoundry中包含一套服务市场功能，通过服务市场我们可以按需获取服务市场中提供的各种资源，例如数据库实例或者一个SaaS服务账号，提供和运营这些资源的系统被称为后端服务，我们获得的这些资源被称为服务实例。如果服务市场中没有我们需要的服务，cloudfoundry允许我们使用[User-Provided Service Instances](http://docs.cloudfoundry.org/devguide/services/user-provided.html) (UPSI)将自己已有的服务集成进来。

![V2services new](http://docs.cloudfoundry.org/services/images/managed-services.png)

为了在一个服务中体现级别差异，cloudfoundry在服务中提供了计划（plan）的概念，计划就和话费套餐一样，标识了在当前计划下的服务中所包含的资源量，例如存储空间的多少，服务级别的高级，服务实例是否共享等

### 后端服务信息的注入VCAP_SERVICES

cloudfoundry的服务中有一类可绑定服务，主要是指那些需要在应用中使用的后端服务，这类服务在cloudfoundry中与应用绑定后，cloudfoundry会将服务信息添加到一个叫`VCAP_SERVICES`的环境变量中，这个环境变量是一个JSON文本格式的字符串，其中包含了后端服务的必要连接信息，例如用户名、密码、服务地址、服务端口、服务的控制台地址等，应用程序可以通过解析环境变量来获知如何联系后端服务

下面是一个绑定了若干服务实例后的VCAP_SERVICES的实例

```
VCAP_SERVICES=
{
  "elephantsql": [
    {
      "name": "elephantsql-c6c60",
      "label": "elephantsql",
      "tags": [
        "postgres",
        "postgresql",
        "relational"
      ],
      "plan": "turtle",
      "credentials": {
        "uri": "postgres://exampleuser:examplepass@babar.elephantsql.com:5432/exampleuser"
      }
    }
  ],
  "sendgrid": [
    {
      "name": "mysendgrid",
      "label": "sendgrid",
      "tags": [
        "smtp"
      ],
      "plan": "free",
      "credentials": {
        "hostname": "smtp.sendgrid.net",
        "username": "QvsXMbJ3rK",
        "password": "HCHMOYluTv"
      }
    }
  ]
}
```

### 定制后端服务servicebroker

CloudFoundry后端服务市场最大的特点就是服务市场中的服务项与CloudFoundry平台本身是解耦的，这样就就极大的提高了平台的开放性。为了避免各类不同服务在管理和运维上的差异性影响到服务市场的开放性，CloudFoundry定义了一套ServiceBroker API规范，根据ServiceBroker API，服务平台在将服务接入CloudFoundry平台时需要提供一个Broker来屏蔽服务管理的细节，然后CloudFoundry与Broker之间的流程被抽象成编目、供给、绑定三个过程，这就是一个应用在使用一个服务的时候最基本的流程。从Broker的开发上来看，Broker是一个按照ServiceBroker API规范开发的HTTP服务，CloudFoundry平台作为客户端会按照ServiceBroker API规范向Broker发起编目、供给、绑定请求，Broker在收到相应的请求后又会根据每个服务的特性来操作服务平台以达成请求的效果。

![V2services new](http://docs.cloudfoundry.org/services/images/v2services-new.png)

**编目**，编目是broker的一系列接口中的从使用角度来说的第一个接口，通过编目接口CloudFoundry的CloudController模块就可以获知目前所有的服务信息，并将它们保存在CloudController的数据库中。

**供给**，当CloudFoundry收到创建服务实例的请求时，会通过CloudController请求供给接口来让broker开始生成一个新的服务实例，这时broker可以通过异步和同步两方式来完成来响应CloudController的请求，根据用户所选项目的不同，服务实例也会产品不同的结果，例如

- 一个完全独占的运行mysql的虚拟服务器
- 一个完全独占的运行mysql的容器
- 一个mysql数据库
- 一个mysql数据库模式
- 一个还有特定数据的mysql数据库

**绑定**，在已经创建了服务实例之后，对于那些可以用来绑定应用的服务，我们可以在需要的时候把服务实例绑定到应用中，这是绑定接口可以生成服务的鉴权凭证，例如密码、证书等，用来让应用访问时鉴权使用。

## CloudFoundry服务发现和路由

Cloudfoundry提供的面向应用的服务发现主要依靠向应用中注入环境变量实现，主要用在应用连接后端服务的场景，在之前介绍的后端服务内容中可以看到Cloudfoundry通过更新容器中的VCAP_SERVICES环境变量来帮助应用方便的找到需要的服务资源。

Cloudfoundry内部的服务发现主要依靠consul、BBS组件完成，他们的区别在于consul中注册的信息主要是长期存在于平台的控制信息，例如每个组件的IP地址，以及用来防止组件间重复响应的分布式锁等。在BBS（Bulletin Board System）中存储的信息则是那些频繁刷新和一次性使用的数据，例如应用状态，心跳消息，为分配的任务，在目前的cloudfoundry系统中BBS系统是通过etcd实现的。

为了方便外部访问部署在cloudfoundry中的应用，cloudfoundry平台提供了router组件来完成流量转发，router将用户请求和应用地址进行映射，router可以知道应用的实例在cloudfoundry中的位置，如果应用在cloudfoundry中采用多实例部署时，router可以使用轮询方法向应用转发请求。同时我们还是在router下绑定多个应用，这样router就可以在多个应用间进行负载均衡，这样就可以实现蓝绿部署，同样的我们还可以给一个应用绑定多个router来满足实际生产过程中的要求。

## 开发管理



## CloudFoundry可用性管理

### Scaling Horizontally

无状态的应用可以很方便的使用cloudfoundry提供的横向扩展功能来提升应用可用性，cloudfoundry使用router在多个应用实例中进行负载均衡，每个应用实例可以平行处理所有请求。一定程度上来说增加应用实例，应用就可以处理更多的请求。

### Blue-Green Deployment

在发布应用时采用蓝绿部署可以缩短中断时间、减少业务风险，具体来说发布前“蓝”版程序正常运行，负责处理所有生产流量，当我们准备一个新的发布时，我们可以在cloudfoundry中部署完整的“绿”版程序，当我们对绿版程序完成必要的测试和验收后，我们可以将“蓝”版程序的router映射到“绿”版程序，来让两个版本的程序都可以接收到流量，之后就可以再对“绿”版程序进行必要的验证，最后就可以取消router到“蓝”版程序的映射关系，最终实现版本的更迭。

## 编排

CloudFoundry有Diego、DEAs（Droplet Execution Agents）两种架构来管理应用容器，Diego可以同时用来承载buildpack和docker镜像，下面我们来看看Diego是如何工作的

### 用Diego承载buildpack应用

![App push flow diagram diego](https://docs.cloudfoundry.org/concepts/images/app_push_flow_diagram_diego.png)

1. 用户进入本地应用目录，然后通过CloudFoundry的命令行工具cf行执行发布命令
2. 客户端会先请求CloudController组件，让CloudController创建一条应用记录，
3. 这时CloudController会将应用名称、实例数量、buildpack等应用元数据存储到数据库中
4. 紧接着cf客户端会请求一次资源对比，来判断应用文件是否已经被上传过，如果文件曾经被上传过，cf客户端就会自动跳过，剩余被上传的文件系统会与缓存中的文件一起打包，形成应用程序包。
5. CloudController将应用程序包存入对象存储系统中
6. 然后cf客户端发出应用启动命令
7. CloudController收到应用启动的命令后，会向Diego模块发出加载应用指令，Diego会调度一个“小单元”（Cell）来运行加载任务，加载任务会下载buildpack，如果有被缓存的builpack还会从重缓存中下载，之后根据buildpack中的定义来启动应用。
8. Diego小单元会在应用加载过程中输出加载进度信息和日志，用户可以以此来解决应用加载过程中的问题。
9. 加载任务会以tar包的形式打包应用程序，打好的包在cloudfoundry被称为“droplet”，然后Diego小单元会将打包好的应用程序存入对象存储中，同时加载任务会把buildpack上传道缓存中以备下次应用加载使用。
10. 之后Diego的BBS（Bulletin Board System）会向CloudController报告加载完成，默认情况下应用加载必须在15分钟内完成，否则应用加载就会被认为加载失败，在加载阶段应用会至少被分配1G内存，尽管应用申请的内存没有那么多。
11. 然后Diego将以长期运行进程形式来调度应用程序到一个或多个Diego小单元
12. 最后Diego小单元给CloudController报告应用状态。


### Diego是如何加载Docker镜像的

![Docker push flow diagram diego](https://docs.cloudfoundry.org/concepts/images/docker_push_flow_diagram_diego.png)

1. 在命令行中使用cf客户端执行push命令，后面跟一下可用的docker仓库和镜像名
2. cf命令行会通知CloudController为push的镜像名创建一个记录
3. 然后CloudController会向Diego发出一个加载命令，Diego在收到消息后会调度一个“小单元”来运行加载任务
4. Diego小单元会输出加载过程信息和日志来帮助用户处理问题
5. 加载任务会获取docker镜像的元数据信息，同时给CloudController返回一个“portion”，CloudController将其存入CCDB。
6. CloudController使用Docker镜像的元数据来创建一个长期运行的进程，同时CloudController还会通过获取用户配置信息来覆盖docker默认配置，例如特定环境变量等。
7. CloudController会提交一个长期运行京城给Diego，Diego将这个长期运行进程调度到一个或多个Diego小单元上。
8. CloudController这时会让Diego和Gorouter将流量路由到Docker容器中。


## CloudFoundry资源管理和调度

### CloudFoundry是如何均衡它的负载的

CloudFoundry可以在多个机器中间进行负载均衡以应对单点故障。整个CloudFoundry大致可以分为三个级别的高可用保障手段。首先BOSH会在CloudFoundry集群物理机上创建和部署虚拟机，在一个配置文件的支持下用虚拟机上部署CloudFoundry，其次CloudFoundry的CloudController在虚拟机中运行应用程序和其他组件，控制请求并且管理生命周期，再次router模块将输入流量路由到运行着应用的虚拟机上，通常与一个用户提供的负载均衡一起工作。

### How Apps Run Anywhere

### 应用程序如何随处运行

CloudFoundry集群中包含两类虚拟机，一类用来运行CloudFoundry组件，构成平台的基础设施，另一类用来运行应用程序。Diego系统会将承载在CloudFoundry中的应用分布在应用程序虚机上，保持应用持续运行，并且在各种情况下保持应用程序虚机的负载均匀。

## CloudFoundry租户管理

在CloudFoundry中资源的管理被分成若干层级，

### 组织

组织是CloudFoundry中相关资源，配额、应用、可用的服务还有自定义的域名等，的管理单元，也就是CloudFoundry中的租户，一个组织可以被一个或多个用户使用，这些用户共享组织中的资源。组织在默认情况下处于“激活”状态，但是管理员可以在欠费、使用不当等情况下将组织状态设置为“挂起”，当组织被挂起后用户就不能在组织中发布进行应用、绑定服务等操作。

### 工作区

一个组织至少包含一个工作区，每个应用和服务都属于一个工作区，工作区提供了一个界面用来进行应用开发、部署和维护。

### 用户账户

用户账户代表着一个实际使用CloudFoundry的个人，一个用户可以在一个组织内的不用工作区内拥有不同的角色。

### 配额计划

配额计划是一组可用内存数、可用服务数以及可用实例数的组合。例如一个配合计划可能包含10个服务，10个路由以及2G内存，同时另外一个可能包含100个服务，100个router，10G内存。通常配合计划都有一个非常友好的名字标示，但是在CloudFoundry内部仅仅是一个GUID。配额计划是与组织关联，而不是与用户账号关联，组织管理员可以在一系列的配额计划中选择一个并与组织关联，组织中的用户共享配额。同时这了要注意的是组织必须具有一个配额计划，但是可以选择是否给工作区分配配额计划。





