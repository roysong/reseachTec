# 开发环境准备
>JGraphT从版本1.0.0开始需要JDK最低1.8,低于1.0.0的版本能够支持1.8以前的JDK/JRE，但功能更少一些。最新的API文档能够[在线](http://www.jgrapht.org/javadoc)查看。指定版本的离线包可以[下载](https://sourceforge.net/projects/jgrapht/files/JGraphT/)。  
>在MAVEN工程中添加JGraphT的方法和其它依赖包一样，在pom.xml中添加如下依赖即可：  
``` xml
<dependency>
  <groupId>org.jgrapht</groupId>
  <artifactId>jgrapht-core</artifactId>
  <version>1.2.0</version>
</dependency>
```  
>通过MAVEN新建一个JGraphT工程通过如下命令行进行：  
``` bash
mvn archetype:generate -DarchetypeGroupId=org.jgrapht.archetypes -DarchetypeArtifactId=maven-archetype-jgrapht -DarchetypeVersion=1.2.0  
```
