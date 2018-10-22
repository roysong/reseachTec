# 运行demo  
JGraphT包含一个`org.jgrapht.demo`包，里面有一些小demo应用。这些应用提供了一些关于如何使用JGraphT的良好范例。同时，这些范例都拥有对应的测试用例，以展示范例的应用和运行结果（参考[源代码列表](https://github.com/jgrapht/jgrapht/releases)来获取你需要的发行版）。  
那么如何从下载的源代码发行版中运行可视化范例呢？在lib目录中执行下面的命令：  
``` java
java -jar jgrapht-demo-x.y.z.jar
```  
要运行不同的范例，可以：  
``` java
java -cp jgrapht-demo-x.y.z.jar org.jgrapht.demo.<DemoName>
比如:
java -cp jgrapht-demo-1.1.0.jar org.jgrapht.demo.CompleteGraphDemo
``` 
如果你想对范例进行更多的控制，比如，你想把玩一下或者做些修改，那么推荐你首先在[源代码列表](https://github.com/jgrapht/jgrapht/releases)中下载需要的版本，或者直接[下载](https://github.com/jgrapht/jgrapht/archive/master.zip)github上主分支。然后，按照你手头IDE的引导导入（跳过有关 Java 格式设置规则和导入样式的说明）：  
* [Intellij IDEA](https://github.com/jgrapht/jgrapht/wiki/How-to-setup-your-development-environment-for-JGraphT#developing-using-intellij-idea)  
* [Eclipse](https://github.com/jgrapht/jgrapht/wiki/How-to-setup-your-development-environment-for-JGraphT#developing-using-eclipse)  
* [Netbeans](https://github.com/jgrapht/jgrapht/wiki/How-to-setup-your-development-environment-for-JGraphT#developing-using-netbeans)  

然后，下面是一些已有的示例代码：  
* [HelloWorld](https://github.com/jgrapht/jgrapht/wiki/HelloWorld)  
* [DependencyDemo](https://github.com/jgrapht/jgrapht/wiki/DependencyDemo)  
* [DirectedGraphDemo](https://github.com/jgrapht/jgrapht/wiki/DirectedGraphDemo)  
* [LabeledEdges](https://jgrapht.org/guide/EX-LabeledEdges)  

[返回目录](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/apply/dev#jgrapht%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97%E6%80%BB%E7%BA%B2)
