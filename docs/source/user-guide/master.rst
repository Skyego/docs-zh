资源管理节点
====================

Master负责管理ChubaoFS整个集群，主要存储5种元数据，包括：数据节点、元数据节点、卷、数据分片、元数据分片。所有的元数据都保存在master的内存中，并且持久化到RocksDB。
多个Master之间通过raft协议保证集群元数据的一致性。注意：master的实例最少需要3个

系统特性
---------------

- 多租户，资源隔离
- 多个卷共享数据节点和元数据节点,每个卷独享各自的数据分片和元数据分片
- 与数据节点和元数据节点即有同步交互，也有异步交互，交互方式与任务类型相关。

配置参数
--------------

ChubaoFS 使用 **JSON** 作为配置文件的格式.

.. csv-table:: 属性
   :header: "配置项", "类型", "描述", "是否必需", "默认值"
   
   "role", "字符串", "进程的角色，值只能是 master", "是"
   "ip", "字符串", "主机ip", "是"
   "listen", "字符串", "http服务监听的端口号", "是"
   "prof", "字符串", "golang pprof 端口号", "是"
   "id", "字符串", "区分不同的master节点", "是"
   "peers", "字符串", "raft复制组成员信息", "是"
   "logDir", "字符串", "日志文件存储目录", "是"
   "logLevel", "字符串", "日志级别", "否", "error"
   "retainLogs", "字符串", "保留多少条raft日志.", "是"
   "walDir", "字符串", "raft wal日志存储目录.", "是"
   "storeDir", "字符串", "RocksDB数据存储目录.此目录必须存在，如果目录不存在，无法启动服务", "是"
   "clusterName", "字符串", "集群名字", "是"
   "exporterPort", "整型", "The prometheus exporter port", "否"
   "consulAddr", "字符串", "consul注册地址，供prometheus exporter使用", "否"
   "metaNodeReservedMem","字符串","元数据节点预留内存大小，单位：字节", "否", "1073741824"



**Example:**

.. code-block:: json

   {
    "role": "master",
    "id":"1",
    "ip": "10.196.59.198",
    "listen": "17010",
    "prof":"17020",
    "peers": "1:10.196.59.198:17010,2:10.196.59.199:17010,3:10.196.59.200:17010",
    "retainLogs":"20000",
    "logDir": "/cfs/master/log",
    "logLevel":"info",
    "walDir":"/cfs/master/data/wal",
    "storeDir":"/cfs/master/data/store",
    "exporterPort": 9500,
    "consulAddr": "http://consul.prometheus-cfs.local",
    "clusterName":"chubaofs01",
    "metaNodeReservedMem": "1073741824"
   }


启动服务
-------------

.. code-block:: bash

   nohup ./cfs-server -c master.json > nohup.out &
