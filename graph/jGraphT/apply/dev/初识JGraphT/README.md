# 初识JGraphT
在JGraphT中，一张图由多条连线连接的多个顶点构成。实际上，在这个基础定义之上还存在很多扩展类型，之后我们会一一道来，但目前让我们先看一个创建有向图的简单例子：  
``` java
import org.jgrapht.*;
import org.jgrapht.graph.*;
import java.net.*;

        Graph<URL, DefaultEdge> g = new DefaultDirectedGraph<>(DefaultEdge.class);

        URL google = new URL("http://www.google.com");
        URL wikipedia = new URL("http://www.wikipedia.org");
        URL jgrapht = new URL("http://www.jgrapht.org");

        // 添加顶点
        g.addVertex(google);
        g.addVertex(wikipedia);
        g.addVertex(jgrapht);

        // 添加连线
        g.addEdge(jgrapht, wikipedia);
        g.addEdge(google, jgrapht);
        g.addEdge(google, wikipedia);
        g.addEdge(wikipedia, google);
```  
注意上图的顶点对象是[java.net.URL](https://docs.oracle.com/javase/8/docs/api/java/net/URL.html)的实例。JGraphT本身并不提供顶点的类定义，相应地，在规则允许的情况下，你可以根据你的应用需求自由选择最适合的对象做为顶点。  
同样你也能自由选择连线的对象，如果你不需要在连线上添加任何指定的业务信息，那么可以如同上面的例子一样直接使用库内置的[DefaultEdge](https://jgrapht.org/javadoc/org/jgrapht/graph/DefaultEdge.html)对象。图形构造方法接收连线的类型作为参数，因此它能够在`addEdge`方法被调用时隐式创建新的连线对象以连接两个顶点。  
[返回上一级](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/apply/dev#jgrapht%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97%E6%80%BB%E7%BA%B2)
