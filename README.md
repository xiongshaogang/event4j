`event4j`是一个实现了`事件驱动`和`异步化`的轻量级框架，可帮助我们实现以事件驱动为中心的应用，采用分派事件和监听事件的方式，使处理事务的上下游各个组件之间不直接发生交互，降低耦合，扩展方便。另外，event4j让事务的每一个处理环节都是异步化且并行执行，使应用的吞吐量大大提升。

实现一个功能时，`传统`的做法是`串行执行`一系列的操作。例如：一个用户登陆的功能，在校验账号和密码成功后，还会进行以下操作：
* 写业务统计日志。
* 通知相关的组件，某个账号登陆了，并且将账号、登陆时间、登陆的环境信息都发送过去。
* 将一条记录到账号的登陆历史中。

我们的做法通常是这样的：在成功校验账号和密码后，再写一堆代码来实现上述的三个操作。所有的操作都是串行的，如下图所示：
![账号登陆的普通实现](http://img0.ph.126.net/_ER36d2FiONGuOsq2E7SDw==/1135751531045032521.png)

这样做没错，但随着系统的演进，为了统计需要或相关联系统的业务需要做的一些额外操作，不断地在功能点中增加许多与用户登陆无关但有必要的操作。这样，在账号登陆的功能中掺入了许多实现额外功能的代码，使之变得**臃肿**。而且，在系统并发量高的情况下会带来一个问题：账号登陆**变慢**。

面对变量的问题，我们就会开始实现异步化（推荐用[threadpool4j](https://github.com/aofeng/threadpool4j)），把这些额外的操作变成异步任务，减少处理时间，提升账号登陆的响应速度。
把一些操作异步化后，代码的主线逻辑更加清晰，臃肿的情况有很大改善。但还是会有不少处理异步任务相关的代码在其中，那有没有可能更进一步，在用户的登陆操作中不出现这些异步任务的代码呢？答案是：**用事件驱动**。

用event4j可以非常优雅地解决这个问题，不仅使处理账号登陆的代码变得非常简洁，而且将来扩展方便。首先，将账号及其相关信息生成一个事件并分派出去，然后写三个处理器监听这个事件即可，如果后续还有类似的在账号登陆成功后要做一些额外的操作，再写一个处理器监听这个事件就行。这样，账号登陆的功能中不再包含实现额外操作的代码，并且通过event4j将登陆操作与其他的额外操作解耦。如下图所示：
![账号登陆使用event4j实现](http://img2.ph.126.net/pQ5M0xIQlYQ8B0M_TPMz7A==/3098476543648143866.png)

`event4j`的文档：

1、[编译event4j](https://github.com/aofeng/event4j/wiki/%E7%BC%96%E8%AF%91event4j)。

2、[event4j入门指南](https://github.com/aofeng/event4j/wiki/event4j%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97)。
