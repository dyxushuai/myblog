在云环境中，DBaas无疑比手动自建数据库拥有更灵活的管理、更好的数据保障措施、更集中的管理面板、更高效的监控以及更快速高效的提供实例，是云解决方案中，完备的生态不可或缺的一环。在于决定提供Database as a service后，基于现状，探索如何选择技术落地的问题。

近期调研了一番市面上的云数据库以及开源的云数据库，这里还需要区分一些概念，我指的云数据库应该具备租户隔离，而市面上开源的云数据库应该是指分布式数据库，是**不支持多租户**的。

目前来讲dbaas有这些玩法：
- 阿里云、AWS、AZURE、google cloudSQL、oracle cloud等提供的公有云数据库服务，都是收费且不开源的，aliyun、azure一直在模仿AWS做，但aliyun摸透了这套玩法后，不断的在扩展dbaas的版图，而oracle只在玩自己的RDS，google只支持mysql、postgresql，还没上路
- 类似elephantSQL，对公网开放了dashboard，注册后创建postgresql时选择一家合作的公有云
- 利用docker做数据库应用编排。Github上有200以下star的dbaas方案，利用容器来做dbaas，乐视云数据库也选择docker，但是从市场反馈来看（eleme CTO），需要数据库绝对安全的需求暂时无法适应docker的无状态，数据的安全性太重要
- 类似青云，通过openstack来做数据库服务编排，其本质上是openstack trove

目前为止，还没有发现哪个开源云数据库，能像trove这般，与openstack生态完美融合。trove项目的发起者是tesora这家公司，CEO精通不下8种数据库，在抽象出其中的共性后，构成了trove管理框架。因此trove既支持mysql等关系型数据库，又支持mongo、redis等nosql。**借助openstack的管理优势，ceph的存储保障，trove的架构设计是逻辑自洽的**。在tesora被以色列一家公司收购、CTO出走verison后，trove应该不会再出现大规模的设计重构、代码重构。如果我们继续用openstack建云，依然需要提供云数据库情况下，我认为trove是最合适的，毕竟没其他项可选择

那么我们如何利用trove，架构出如阿里云数据库那般的DBaas？通过阿里云DBaas提供的功能特性，再看看trove能做一些什么。目前trove相对于阿里云，有些功能落后或者缺失了，但**二者还是同一个纬度的东西**。也许阿里云觉得当前架构已够用，将来只是细节做些优化，那么trove也许还能继续扛一阵子。又或许阿里云再搞个巨大的技术变革，相比与trove提升了若干个纬度，那就看市场能承受的价格、技术的吸纳能力。未来不好预判，只是当下，trove是一个相对较好的选择

在详细对比阿里云RDS、trove之后，他们的功能重合度达到90%以上，后续再给出详细对比结果
