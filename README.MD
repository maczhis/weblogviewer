# 通过浏览器实时的查看服务器日志
****
## 依赖：
- python2.7
- gevent
- tornado
- setproctitle
- gevent-websocket

## 系统组成：
- 首先有个中心服务器，通过python Start.py来启动，它总共有4个进程，main进程，config进程，logcenter进程，view进程，以为基于了一个比较轻量级的python分布式框架来做的，所以中心服务器需要启动多个进程，
> 稍微讲解一下，因为中心服务以多进程方式启动，所以mian进程是一个主进程，其余都是其启动的子进程，其中config进程是整个集群的配置中心，center进程是用于管理注册的日志进程信息，view进程负责提供http以及websocket的接口。

- 对于需要监控的服务器，需要启动一个Node进程，通过python StartNode.py来启动，它用于负责提供服务给前面提供的中心服务，上面提到的中心服务会通过rpc的方式来通过Node进程来获取需要监控的log日志的数据然后显示到web界面上去

具体的操作过程我会写到我的CSDN博客上去，毕竟那个上面发图片什么的比较的方便。
> 使用可以参考: http://blog.csdn.net/fjslovejhl/article/details/51152786

**功能现在还比较简单，只有tail的功能，以后有需求的话可能会加上grep之类的功能吧，web界面基本也都是拼拼凑凑，其实也都是作为小工具用用，所以要求不高。**

> 有任何问题可以通过邮件联系我：neufjs@gmail.com

--------------------------------------------
### 补充一下关于端口开放的问题：

1. 对于日志节点，也就是 StartNode 启动的进程，需要开放它的9000端口给中心系统用，因为中心系统需要直接建立连接到Node进程所在的机器
的这个端口，StartNode.py里面写死的，改代码就是了

2. 对于中心系统，因为会同时向外开放http和websocket的接口，在ServerConfig里面需要写一个HTTP_PORT端口，例如9000端口，另外对于websocket
端口就是9000端口的基础上加上50，也就是9050，那么整个系统想要通过web访问的话，就需要同时开放9000端口和9050端口。。。这个是在
Start.py里面写死的，可以自己改代码就是了

3. 对于日志节点，还需要能够访问中心系统的5670端口，log_center_worker = LogCenterWorker(5670, config_address) 也是代码写死的
