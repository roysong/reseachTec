# 从oracle数据表中导出数据到mongodb
## 背景
----------------------
目前（2018）我们的信息系统面临从传统的关系型数据库架构过渡到分布式大数据架构，这个过程中必然涉及从RDBMS到mongodb的数据迁移。  
## 思路
----------
从oracle中导出数据文件，再使用mongodb自身的导入工具进行文件导入。
## 方法
----------
oracle能够将表数据导出为多种文件格式，根据实践检验，采用csv文件格式最为简单，所以此处以csv文件导出方式为主。
### 1.从oracle的数据表中导出数据为csv文件
在sqldeveloper中选中需要导出的表，右键菜单选中export
![在sqldeveloper中选中需要导出的表](https://github.com/roysong/reseachTec/raw/master/bigData/static/mongo/sqldev.png)
然后在弹出窗口中执行导出
![执行导出](https://github.com/roysong/reseachTec/raw/master/bigData/static/mongo/exportWindow.png)
### 2.将csv文件上传至mongodb所在的服务器
命令方式：
scp root@192.168.0.142:/home/roysong/AUTUMN_DEVICE_.csv /root
或者采用SSH SECURY及其它工具将csv文件上传到服务器上
### 3.使用mongoimport将csv文件导入
首先以SSH方式登录到mongodb所在的Linux服务器上，并切换到mongodb安装目录下的bin目录中，如：   
cd /opt/extend/mongo/mongodb-linux-x86_64-3.6.4/bin/  
然后使用bin目录中的mongoimport命令执行导入，如：  
./mongoimport --db guangtieautumn --collection AUTUMN_DEVICE_ --type csv --headerline --file /root/AUTUMN_DEVICE_.csv
注意，此处是将数据导入到当前服务器上，如果要导入另外一台服务器上，则需要加其它参数，详情可参见Mongo官方文档：https://docs.mongodb.com/manual/reference/program/mongoimport/  
--db 指定的数据库名  
--collection 指定的表名  
--type csv 指定导入的数据文件是csv格式  
--headerline 指定csv中第一行是列名  
--file 指定需导入的csv文件路径  

经实践证明，使用mongoimport可以将csv数据导向mongos入口，并存储在分布式系统中，导入命令多加了host、port和ignoreBlanks参数：  
./mongoimport --host 192.168.0.92 --port 27030 --db rinms --collection RES_RESOURCE_DEVICE --type csv --headerline --ignoreBlanks --file /root/hengyang_ad/res_resource_device.csv  
--host 主机ip  
--port 对应端口，此处的27030是Mongos的端口  
--ignoreBlanks 这个参数可以忽略掉 CSV 文件中的空缺值  
