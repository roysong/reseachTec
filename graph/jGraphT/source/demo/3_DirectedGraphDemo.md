# DirectedGraphDemo  
``` java
import org.jgrapht.*;
import org.jgrapht.alg.connectivity.*;
import org.jgrapht.alg.interfaces.ShortestPathAlgorithm.*;
import org.jgrapht.alg.interfaces.*;
import org.jgrapht.alg.shortestpath.*;
import org.jgrapht.graph.*;

import java.util.*;

/**
 * 本类中演示了针对有向图的一些操作。首先是如何构造一张基本的有向图，然后计算得出此图中所有的强连接
 * 组件。最后采用迪杰斯特拉算法找出两个顶点之间的最短路径。
 * 本示例能够帮助用户理解如何在有向图中使用org.jgrapht.alg.shortestpath.DijkstraShortestPath类来寻
 * 找最短路径。
 * @author Minh Van Nguyen
 */
public class DirectedGraphDemo
{
    /**
     * 应用主入口
     *
     * @param args ignored.
     */

    public static void main(String args[])
    {
        // 使用指定的顶点和连线构建一张有向图
        Graph<String, DefaultEdge> directedGraph =
            new DefaultDirectedGraph<String, DefaultEdge>(DefaultEdge.class);
        directedGraph.addVertex("a");
        directedGraph.addVertex("b");
        directedGraph.addVertex("c");
        directedGraph.addVertex("d");
        directedGraph.addVertex("e");
        directedGraph.addVertex("f");
        directedGraph.addVertex("g");
        directedGraph.addVertex("h");
        directedGraph.addVertex("i");
        directedGraph.addEdge("a", "b");
        directedGraph.addEdge("b", "d");
        directedGraph.addEdge("d", "c");
        directedGraph.addEdge("c", "a");
        directedGraph.addEdge("e", "d");
        directedGraph.addEdge("e", "f");
        directedGraph.addEdge("f", "g");
        directedGraph.addEdge("g", "e");
        directedGraph.addEdge("h", "e");
        directedGraph.addEdge("i", "h");

        // 计算有向图中所有的强连接组件
        StrongConnectivityAlgorithm<String, DefaultEdge> scAlg =
            new KosarajuStrongConnectivityInspector<>(directedGraph);
        List<Graph<String, DefaultEdge>> stronglyConnectedSubgraphs =
            scAlg.getStronglyConnectedComponents();

        // 打印出所有的强连接组件
        System.out.println("Strongly connected components:");
        for (int i = 0; i < stronglyConnectedSubgraphs.size(); i++) {
            System.out.println(stronglyConnectedSubgraphs.get(i));
        }
        System.out.println();

        // 打印出从顶点i到顶点c的最短路径，已知这条路径必然存在
        // exists for our particular directed graph.
        System.out.println("Shortest path from i to c:");
        DijkstraShortestPath<String, DefaultEdge> dijkstraAlg =
            new DijkstraShortestPath<>(directedGraph);
        SingleSourcePaths<String, DefaultEdge> iPaths = dijkstraAlg.getPaths("i");
        System.out.println(iPaths.getPath("c") + "\n");

        // 打印出从顶点c到顶点i的最短路径，已知这条路径实际并不存在
        // 预计输出结果为null
        System.out.println("Shortest path from c to i:");
        SingleSourcePaths<String, DefaultEdge> cPaths = dijkstraAlg.getPaths("c");
        System.out.println(cPaths.getPath("i"));
    }
}
```  
本示例演示了有向图的生成，以及计算[强连接](https://blog.csdn.net/caipeichao2/article/details/32157989?utm_source=tuicool&utm_medium=referral)组件（Strongly connected components）和采用[迪杰斯特拉算法（dijkstra）](https://baike.baidu.com/item/%E8%BF%AA%E6%9D%B0%E6%96%AF%E7%89%B9%E6%8B%89%E7%AE%97%E6%B3%95)计算最短路径的方法。  
本示例基本上还是开发指南中的代码实例，[最短路径算法包](https://jgrapht.org/javadoc/org/jgrapht/alg/shortestpath/package-summary.html)中有近十种算法实现，但此处只讲了一最简单的一种。而最短路径常见的算法有[四种](https://www.cnblogs.com/luweiseu/archive/2012/07/14/2591533.html)，这儿也只介绍了一种而已。所以，此示例的内涵极其有限，适用场景也较窄。建议上面加链接的部分，都点进去学习了解一下吧。  
