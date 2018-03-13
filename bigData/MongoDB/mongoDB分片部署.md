# MongoDB分片部署

## 部署前准备
MongoDB3.6以后，分片必须是副本集，同时配置服务建议3个，所以也需配置成副本集，此处配置两个shard server一个config server一个route，所以需要启动7个mongo实例。
### 部署配置如下：
* 虚拟机1：
    - route：27017
    - config server：27018,27019,27020
    - shard server：27021，27022,27023
* 虚拟机2：
    - shard server：27018,27019,27020

### 准备过程
1. 在虚拟机上创建实例所需文件夹
```
for i in 17 18 19 20 21 22 23
do
mkdir -p /data/db/270$i/conf
mkdir -p /data/db/270$i/data
mkdir -p /data/db/270$i/log
done
```
2. shard 配置文件
``` yml
systemLog:
  destination: file
  path: /data/db/27021/log/mongodb.log
  logAppend: true
storage:
  journal:
    enabled: true
  dbPath: /data/db/27021/data
  directoryPerDB: true
  #engine: wiredTiger
  wiredTiger:
    engineConfig:
      cacheSizeGB: 1
      directoryForIndexes: true
    collectionConfig:
      blockCompressor: zlib
    indexConfig:
      prefixCompression: true
net:
  bindIp: 0.0.0.0
  port: 27021
replication:
  oplogSizeMB: 2048
  replSetName: sh1
sharding:
  clusterRole: shardsvr
processManagement: 
  fork: true
```
    * 分发配置文件，并快速修改配置文件中的端口号
        ```
        for i in 22 23
        do
        cp /data/db/27021/conf/mongod.conf /data/db/270$i/conf
        done

        for  i in   22 23 24 25 26  
        do 
        sed  -i  "s#27021#270$i#g" /data/db/270$i/conf/mongod.conf
        done
        ```

3. conf 配置文件
```
systemLog:
  destination: file
  path: /data/db/27018/log/mongodb.conf
  logAppend: true
storage:
  journal:
    enabled: true
  dbPath: /data/db/27018/data
  directoryPerDB: true
  #engine: wiredTiger
  wiredTiger:
    engineConfig:
      cacheSizeGB: 1
      directoryForIndexes: true
    collectionConfig:
      blockCompressor: zlib
    indexConfig:
      prefixCompression: true
net:
  bindIp: 0.0.0.0
  port: 27018
replication:
  oplogSizeMB: 2048
  replSetName: configReplSet
sharding:
  clusterRole: configsvr
processManagement: 
  fork: true
```
同上分发和批量修改

4. mongos节点配置
```
systemLog:
  destination: file
  path: /data/db/27017/log/mongos.log
  logAppend: true
net:
  bindIp: 0.0.0.0
  port: 27017
sharding:
  configDB: configReplSet/127.0.0.1:27108,127.0.0.1:27019,127.0.0.1:27020
processManagement: 
  fork: true
```

## 部署过程
1. 启动分片服务（Shard Server）
```
# 启动分片服务
for i in 18 19 20
do
/var/local/mongodb-linux-x86_64-3.6.2/bin/mongod -f /data/db/270$i/conf/mongod.conf
done

# 配置副本集
mongo --host 127.0.0.1 --port 28021  admin

var config = {_id: 'sh1', members: [
                  {_id: 0, host: '192.168.0.230:27021'},
                  {_id: 1, host: '192.168.0.230:27022'},
                  {_id: 2, host: '192.168.0.230:27023',"arbiterOnly":true}
                  ]
              }
# 初始化配置
rs.initiate(config)
```
2. 启动配置服务 （Config Server）
同上(_id:'configReplSet')
> 注：config server 使用复制集不用有arbiter节点。3.4版本以后config必须为复制集

3. 启动路由 （Route Process）
```
mongos -f /data/db/27017/conf/mongod.conf
```

4. 配置Sharding
```
#登陆到mongos节点
mongo 127.0.0.1:27017/admin

#添加分片节点
db.runCommand( { addshard : "sh1/192.168.0.230:27021,192.168.0.230:27022,192.168.0.230:27023",name:"shard1"} )
db.runCommand( { addshard : "sh2/192.168.0.178:27018,192.168.0.178:27019,192.168.0.178:27020",name:"shard2"} )

#整体状态查看
sh.status();

```
> 添加失败时注意检查防火墙 (systemctl stop firewalld.service)

## 数据库分片配置
* 激活数据库分片功能
```
语法：( { enablesharding : "数据库名称" } )

mongos> db.runCommand( { enablesharding : "test" } )
```

* 指定分片建对集合分片，范围片键--创建索引
```
mongos> use test 
mongos> db.vast.ensureIndex( { id: 1 } )
mongos> use admin
mongos> db.runCommand( { shardcollection : "test.vast",key : {id: 1} } )
```
> 激活数据库分片功能后，还需指定分片建队集合分片，否则导入的数据不能进行分片