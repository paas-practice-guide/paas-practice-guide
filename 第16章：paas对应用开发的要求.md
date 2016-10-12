PaaS实现了应用开发者和平台维护者之间的分工，应用开发者可以提交代码给PaaS平台，这些代码的运行将按照应用开发者的预期来进行。但例如高可用、扩展性的实现部分每份代码都可能不一样，如果保持原样托管到PaaS中，PaaS云平台最多只能将这些代码运行起来，在遇到故障或需要扩展的时候不知道应该如何处理。

要想尽可能多地由云平台提供便利性，而减少应用开发者需要书写的代码量，这就要求托管到PaaS平台上的代码需要遵守一些规则。但也需要考虑到传统PaaS的缺陷就是对开发者提出了较多的“要求”。所以我们必须在提供便利和提出要求之间找到一个折衷。

有了Docker作为运行时环境，我们已经可以对开发者使用的语言、基础操作系统、库文件和依赖的第三方组件不用提出要求。但对于高可用和扩展性的实现部分，仍需要规定一些代码风格。主要的目的是为了尽可能地实现代码的“无状态”化。

[Heroku](http://www.heroku.com/)是业内知名的PaaS云平台，从对外提供服务以来，他们已经有上百万应用的托管和运营经验。基于这些积累，其创始人Adam Wiggins在2012年发布了一个“十二因子（The Twelve-Factor App）”的规范。参见附录A。Adam Wiggins对应用开发者提出了十二项建议，其实质就是为了实现无状态，如果符合这些规范建议，那么应用在HeroKu等PaaS云平台上将会非常容易，也能充分将平台的功能充分发挥出来。

cloud-native应用的本质是它们满足如下特点的契约：通过可预测的行为，最大化复原。云平台使用的高度自动化、容器驱动的基础设施，引领软件的编写方式。开发者必须改变编码方式，在开发者和运行应用的基础设施之间建立一种新的契约。一个很好的契约例子是十二因素应用。

Google联合其他20家公司宣布成立了开源组织Cloud Native Computing Foundation（CNCF），

CNCF包含21家厂商：AT&T、Box、Cisco、Cloud Foundry Foundation、CoreOS、Cycle Computing、Docker、eBay、Goldman Sachs、Google、Huawei、IBM、Intel、Joyent、Kismatic、Mesosphere、Red Hat、Switch SUPERNAP、Twitter、Univa、VMware and Weaveworks。这么多厂家的支持也反映出了容器市场的火热程度。CNCF也将隶属于Linux基金会管理。

