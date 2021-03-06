---
layout: post
title:  "分布式系统伪 CAP 理论"
date:   2014-08-20 11:55:02
categories: distributed
---

以下内容纯属胡扯！！！

Eric Brewer 教授提出的 CAP 理论对于我来实在是难以理解，看了网上各种解释都没能够完全弄明白...

根据自己的工作经验，我对分布式系统的特性瞎总结了一下：
一个分布式系统难以同时满足以下三个特性：
1.  一致性：很好理解
2.  高可靠性：大概的意思就是不存在类似于单点的问题
3.  数据完整性：集群上的所有资源，要么都能访问，要么都不能访问。而不能容忍只有部分资源能访问

- 主从模式的 MySQL 集群，由于存在多备份冗余，所以保证了"绝对"的高可靠性和高数据完整性
  但是显然，一致性就不能保证了（主从同步延迟等）

- 同一份数据放在不同的节点上，就必然涉及同步问题，就会带来一致性问题。
  将数据只在一台节点上存放就能保证一致性了，如单点的 MySQL 数据库，但是这样显然就带来了可靠性问题
  Hadoop 集群也是一样，通过将元数据信息都放在 namenode 上进行独裁管理，保证了一致性，但是降低了可靠性

- 为了保证一致性的同时，集群不至于出现单点，还有一个解决方案：分表
  将数据分隔成一块一块的，然后分放在不同的节点上，通过一致 hash 等方法进行访问
  首先：由于某个数据只存在唯一一个节点上，所以不会存在一致性问题
  其次：由于不存在单点，挂了一两台节点后，其他的数据还是可访问的
  带来的问题就是部分数据单点，从而无法保证数据完整性问题了
  这个方案还有的好处就是资源利用率比较高、可扩展性比较好。INF 团队改造的 HDFS 就是基于这个思想

其实如果资源是批量更新的（例如 BS 的批量灌库），还有一个可用的解决方案就是：
弄两套服务，一套用于对外服务，一套用于批量"灌库"，灌好了之后，服务再切过来

[jekyll-gh]: https://github.com/jekyll/jekyll
[jekyll]:    http://jekyllrb.com

