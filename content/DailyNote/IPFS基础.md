---
title: "IPFS基础"
date: 2018-12-19T15:37:53+08:00
draft: false
featuredImg: ""
tags: 
  - ipfs
  - blockchain
---
# IPFS是什么：


```
IPFS is a global, versioned, peer-to-peer filesystem. It combines good ideas
from Git, BitTorrent, Kademlia, SFS, and the Web. It is like a single bit-
torrent swarm, exchanging git objects. IPFS provides an interface as simple
as the HTTP web, but with permanence built in. You can also mount the world
at /ipfs.
```
这是ipfs的原话，我的理解是ipfs是一个去中心化的网络系统。它吸取了git，sfs等8个先进有效的方法来管理整个文件系统。去中心化，靠协议和各种共识机制来约定行为是未来互联网的方向；因为这就和电子商务一样，最核心的是能进一步提高效率，节约大量的成本。所以这是大趋势，不可逆转。
<!--more-->

## IPFS怎么搭建一个去中心网络

https://www.jianshu.com/p/48a2739bade2
这里把基本使用说的很详细，我可能会使用一部分，不过我会主要说明技术部分。

![p2p-ipfs](https://lh3.googleusercontent.com/-s9WMbI9Lyjo/W3z8v1XUyZI/AAAAAAAAB4w/L9zacPObl94YVE5f3GniydkCbSUohTnUQCHMYCw/I/p2p-ipfs.jpg)

### IPFS是一个p2p网络，先来看看BitTorrent的p2p网络是如何工作的？

想要bt下载一个文件，首先你需要一个种子文件torrent，种子文件包含至少一个 Tracker（一台服务器地址）信息和文件的分割记录信息。BT软件解析种子文件torrent，从里面找出来tracker，告诉tracker我要下载这个文件。tracker同时返回给你现在其它正在下载的节点信息，这个时候你的节点就正式加入了p2p下载网络，根据tracker返回的信息直接跟其它节点建立联系，开始数据传输。

这种情况下Tracker成为了沟通p2p网络的关键一环，如果tracker服务器全部关闭的话，p2p网络就被关闭了。

那么是不是说如果没有了Tracker，p2p就不能实现了？显然不是的，后来有了DHT（分布式哈希表），DHT技术的出现使得没有tracker也能进行p2p网络下载，用过电驴的读者应该都不陌生，电驴里面有一个选项，可以允许选择KAD（DHT的一种）网络进行搜索。这样一来p2p网络的适应性更强了，可以大大减轻tracker的负担。

以上就是传统的P2P软件大概工作方式。
IPFS的p2p网络使用的是DHT技术

### 什么是DHT ( Distributed Hash Tables ) ?

DHT是一个分布式系统, 它提供了一个类似哈希表一样的查询服务: 键值对存储在DHT中, 任何参与的节点都可以有效的检索给定键对应的值. 键值对的映射由网络中所有的节点维护, 每个节点负责一小部分路由和数据存储. 这样即使有节点加入或者离开, 对整个网络的影响都很小, 于是DHT可以扩展到非常庞大的节点(上千万)。

DHT广泛应用于各种点对点系统, 用来存储节点的元数据。比如：BTC系统使用 MainlineDHT来维护节点。

DHT具有以下性质:

离散型(Autonomy and decentralization): 构成系统的节点之间都是对等的, 没有中央控制机制进行协调
伸缩性(Scalability): 不论系统有多少节点, 都要求高效工作
容错性(Fault tolerance): 不断有节点加入和离开, 不会影响整个系统的工作

### 简单介绍一席跟IPFS有关的三种DHT技术

#### Kademlia DHT

高效查询：查询的平均复杂度是 log2(n)，例如：10,000,000个节点只需要20次查询
低开销：优化了发往其它节点的控制消息的数量
可以抵御各种攻击
广泛应用于各种点对点系统，包括：Guntella和BitTorrent，可以构建超过2千万个节点的网络
#### Coral DSHT

Coral继承了Kademlia并且做了一些改造 ( 它认为直接在DHT上存储数据是浪费存储和带宽)

#### S/Kademlia DHT

S/Kademlia DHT同样是继承了Kademlia，并且做了一些改进，系统可以防止恶意攻击，例如女巫攻击。

### IPFS的路由系统

ipfs系统的节点查找有两个需求：

第一找到其他节点地址
第二找到存储有特定数据的节点

IPFS综合了S/Kademlia 、Coral和Mainline技术（能折腾吧，不过这种对技术的追求精神值得我们给ipfs团队鲜花和鼓掌）。Kademlia协议的工作方式比较复杂, 有兴趣了解的可以单独去查询一下, 本文不在进行更深入的讨论. KAD可以很高效进行路由查询服务。

IPFS DHT的数据存储是根据数据的大小进行的：

小于1KB的数据直接存储到DHT上面
大于1KB的数据在DHT中存储的是节点ID
### 节点加入

IPFS是基于DHT技术的，所以在IPFS网络里面是没有tracker存在的。那么一个新的节点创建后是如何加入网络的呢？

新创建的节点必须知道至少一个已经在网络上的节点地址，连上那个节点，就可以加入网络了，所以ipfs系统提供了bootstrap命令来完成这个工作（通常情况不需要自己来做这样的操作，除非有一些特殊需求，例如：指定自己比较近的启动节点，搭建IPFS私有网络等）


```
ipfs bootstrap list 列出来启动节点
ipfs bootstrap add [<peer>] 添加启动节点
ipfs bootstrap rm [<peer>] 删除启动节点
```

