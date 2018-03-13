# spark环境搭建
## 单机环境搭建
1. 解压安装
2. 设置spark_home（optional）
/etc/profile
```
SPARK_HOME=/opt/spark-2.2.1-bin-hadoop2.7
export SPARK_HOME
export PATH=${PATH}:${SPARK_HOME}/bin
```
3. 主节点配置
    * 修改spark_home/conf/spark-env.sh
    * 在脚本文件中添加JAVA_HOME绝对路径
    * SPARK_MASTER_HOST=192.168.0.230
    * SPARK_MASTER_PORT=7070
    * SPARK_LOCAL_IP=192.168.0.230
4. 启动
    * master启动
    spark_home/sbin/start-master.sh
    * slave启动
    spark_home/sbin/start-slave.sh spark://ip:port
5. 设置主节点到其他节点SSH的无密码登陆
```
执行 ssh-keygen
# 在工作节点上：
# 把主节点的~/.ssh/id_dsa.pub文件复制到工作节点上，然后使用：
$ cat ~/. ssh/ id_dsa.pub > > ~/. ssh/ authorized_keys
$ chmod 644 ~/. ssh/ authorized_keys
```

6. spark运行环境
把本地依赖jar包上传到spark目录的/jars文件夹中

## 分布式环境搭建

1. 设置主节点到其他节点SSH的无密码登陆
```
把主节点的~/.ssh/id_dsa.pub文件复制到工作节点上，然后使用：
$ cat ~/. ssh/ id_dsa.pub > > ~/. ssh/ authorized_keys
$ chmod 644 ~/. ssh/ authorized_keys
```
2. 复制主节点的spark到从节点相同目录，并修改从节点bin和sbin的可执行权限
3. 主节点slave配置中添加从节点ip
4. 主节点启动