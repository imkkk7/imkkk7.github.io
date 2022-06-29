# 一、Zookeeper介绍

## 1.什么是Zookeeper

Zookeeper是一种分布式协调服务，用于管理大型主机。在分布式环境中协调和管理服务是一个复杂的过程。Zookeeper通过其简单的框架和API解决了这个问题。允许了开发人员专注核心应用程序逻辑，不必担心应用程序的分布式特性

## 2.Zookeeper的应用场景

- 分布式协调组件

[![jee7xe.png](https://s1.ax1x.com/2022/06/28/jee7xe.png)](https://imgtu.com/i/jee7xe)

在冗余部署的情况下，我们改变flag = false 另一个服务的值不会改变，而Zookeeper可以帮我们进行改变这个操作

- 分布式锁

Zookeeper在实现分布式锁上，可以做到强一致性

- 无状态化的实现

[![jemYi6.png](https://s1.ax1x.com/2022/06/28/jemYi6.png)](https://imgtu.com/i/jemYi6)

在系统吞吐量较大的情况下，我们需要进行冗余部署，而怎样实现对多个系统的校验，这时候就需要使用Zookeeper

# 二、搭建Zookeeper服务器

## 1.zoo.cfg配置文件说明

[![jeuk40.md.png](https://s1.ax1x.com/2022/06/28/jeuk40.md.png)](https://imgtu.com/i/jeuk40)

## 2.Zookeeper服务器的操作命令

- 重命名conf中的文件zoo_sample.cfg  ->zoo.cfg
- 启动zookeeper服务器：

```
./bin/zkServer.sh start ./conf/zoo.cfg
```

- 查看zookeeper服务器状态

```
./bin/zkServer.sh status ./conf/zoo.cfg
```

- 停止zookeeper服务器

```
./bin/zkServer.sh stop ./conf/zoo.cfg
```

# 三、Zookeeper内部的数据模型

## 1.zookeeper是如何保存数据的

zookeeper中的数据是保存在节点上的，节点就是znode，多个znode之间构成一棵树的目录结构

zookeeper的数据模型是什么样子呢？它很像数据结构当中的树，也很想文件系统的目录

树是由节点所组成，Zookeeper的数据存储也同样是基于节点，这种节点叫做Znode

但是不同于树的节点，Znode的引用方式是路径引用，类似于文件路径

```
/动物/猫
/汽车/宝马
```

这样的层级结构，让每一个Znode节点拥有唯一的路径，就像命名空间一样对不同的信息做出清晰的隔离