

etcd主要分为四个部分:

    HTTP Server： 用于处理用户发送的API请求以及其它etcd节点的同步与心跳信息请求。
    Store：用于处理etcd支持的各类功能的事务，包括数据索引、节点状态变更、监控与反馈、事件处理与执行等等，是etcd对用户提供的大多数API功能的具体实现。
    Raft：Raft强一致性算法的具体实现，是etcd的核心。
    WAL：Write Ahead Log（预写式日志），是etcd的数据存储方式。除了在内存中存有所有数据的状态以及节点的索引以外，etcd就通过WAL进行持久化存储。WAL中，所有的数据提交前都会事先记录日志。Snapshot是为了防止数据过多而进行的状态快照；Entry表示存储的具体日志内容。
    通常，一个用户的请求发送过来，会经由HTTP Server转发给Store进行具体的事务处理，如果涉及到节点的修改，则交给Raft模块进行状态的变更、日志的记录，然后再同步给别的etcd节点以确认数据提交，最后进行数据的提交，再次同步。




# Ref 

https://zhangchenchen.github.io/2017/08/05/intro-to-etcd/

# Useful Operations

```bash
$ etcdctl -v
etcdctl version: 3.2.5
API version: 2
$ ETCDCTL_API=3 etcdctl version
etcdctl version: 3.2.5
API version: 3.2

[root@ose0 lib]# ps aux |grep etcd 
etcd       1064  1.6  0.7 6021280 146848 ?      Ssl  Jul25 428:39 /usr/bin/etcd --name=ose0.cloud.genomics.cn --data-dir=/var/lib/etcd/ --listen-client-urls=https://192.168.122.245:2379
root       2301  0.2  0.2 657380 47084 ?        Ssl  Jul25  73:46 /usr/bin/service-catalog apiserver --storage-type etcd --secure-port 6443 --etcd-servers https://ose0.cloud.genomics.cn:2379,https://ose1.cloud.genomics.cn:2379,https://ose2.cloud.genomics.cn:2379 --etcd-cafile /etc/origin/master/master.etcd-ca.crt --etcd-certfile /etc/origin/master/master.etcd-client.crt --etcd-keyfile /etc/origin/master/master.etcd-client.key -v 10 --cors-allowed-origins localhost --admission-control KubernetesNamespaceLifecycle,DefaultServicePlan,ServiceBindingsLifecycle,ServicePlanChangeValidator,BrokerAuthSarCheck --feature-gates OriginatingIdentity=true
```

先要弄懂几个概念：

    member： 指一个 etcd 实例。member 运行在每个 node 上，并向这一 node上的其它应用程序提供服务。
    Cluster： Cluster 由多个 member 组成。每个 member 中的 node 遵循 raft共识协议来复制日志。Cluster 接收来自 member的提案消息，将其提交并存储于本地磁盘。
    Peer： 同一 Cluster 中的其它 member。


```bash
etcdctl -C $ENDPOINTS
     --ca-file=/etc/origin/master/master.etcd-ca.crt \
     --cert-file=/etc/origin/master/master.etcd-client.crt \
     --key-file=/etc/origin/master/master.etcd-client.key cluster-health

	

[root@ose0 ~]# ./check_etcd_healthy.sh 
member 2b56669455fce48a is healthy: got healthy result from https://192.168.122.14:2379
member 8dadc959d0dca3e6 is healthy: got healthy result from https://192.168.122.245:2379
member f7d2b4f063141c83 is healthy: got healthy result from https://192.168.122.124:2379
cluster is healthy

```
