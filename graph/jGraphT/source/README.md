# JGraphT源码分析
从目录结构及pom.xml中可以看出，JGraphT的源码中包含8个模块：  
1. jgrapht-core:核心功能
2. jgrapht-io:导入导出的功能实现  
3. jgrapht-opt:使用fastutil优化部分策略的版本  
4. jgrapht-ext:针对jgraphx的适配器  
5. jgrapht-guava:针对guava的适配器  
6. jgrapht-demo:运用示例  
7. jgrapht-bundle:打包需要的pom.xml  
8. jgrapht-dist:打独立JAR包的assembly.xml  

从上述模块的说明可以看出，core和io模块是我们源码分析的重点。  
demo模块可以作为功能学习和架构了解的入口。  
opt、ext和guava作为扩展阅读的可选项存在。  
因此，本目录中三个文件夹分别对应demo、core和io模块。  
初学者建议从demo开始。
