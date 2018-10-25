# CompleteGraphDemo  
``` java
import org.jgrapht.*;
import org.jgrapht.generate.*;
import org.jgrapht.graph.*;
import org.jgrapht.traverse.*;
import org.jgrapht.util.SupplierUtil;

import java.util.*;
import java.util.function.Supplier;

/**
 * 演示了如何创建一张完全图并按深度优先顺序进行遍历
 *
 */
public final class CompleteGraphDemo
{
    // 顶点的数量
    private static final int SIZE = 10;

    /**
     * demo主入口
     * 
     * @param args command line arguments
     */
    public static void main(String[] args)
    {
        // 创建顶点工厂用于生成顶点
        Supplier<String> vSupplier = new Supplier<String>()
        {
            private int id = 0;

            @Override
            public String get()
            {
                return "v" + id++;
            }
        };

        // 创建图对象
        Graph<String, DefaultEdge> completeGraph = new SimpleGraph<>(vSupplier, SupplierUtil.createDefaultEdgeSupplier(), false);

        // 创建完全图生成器对象
        CompleteGraphGenerator<String, DefaultEdge> completeGenerator =
            new CompleteGraphGenerator<>(SIZE);

        // 用完全图生成器来生成一张包含[size]个顶点的完全图
        completeGenerator.generateGraph(completeGraph);

        // 打印以辨识是否真的完全图
        Iterator<String> iter = new DepthFirstIterator<>(completeGraph);
        while (iter.hasNext()) {
            String vertex = iter.next();
            System.out.println(
                "Vertex " + vertex + " is connected to: "
                    + completeGraph.edgesOf(vertex).toString());
        }
    }
}
```  
本示例演示如何创建一张完全图，有几个有意思的点：  
1. 在进行图形构建时，没有使用基本的顶点类型，也没有直接使用自定义类作为顶点类型，而是采用了一个顶点构建 器（vSupplier）来构建顶点。  
2. 生成完全图时，直接使用了内置的完全图生成器（CompleteGraphGenerator）来生成完全图。那首先需要明确的是，什么是完全图？在无向图中，若每对顶点之间都有一条边相连，则称该图为完全图；在有向图中，若每对顶点之间都有二条方向不同的有向边相互连接，则称该图为完全图。那么，本示例中生成的完全图是有向的还是无向的呢？留待读者思考，提示，有两个明显的途径能够获知。  
3. 在对完全图进行深度优先遍历时，直接使用了内置的深度优先迭代器（DepthFirstIterator）。那么，[org.jgrapht.traverse](https://jgrapht.org/javadoc/org/jgrapht/traverse/package-summary.html)包下对应的迭代器还有哪些呢？[广度优先迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/BreadthFirstIterator.html)、[距离优先迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/ClosestFirstIterator.html)、[交叉连接迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/CrossComponentIterator.html)、[退化顺序迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/DegeneracyOrderingIterator.html)、[深度优先迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/DepthFirstIterator.html)、[字典宽度优先迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/LexBreadthFirstIterator.html)、[最大势算法迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/MaximumCardinalityIterator.html)、[随机遍历迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/RandomWalkIterator.html)、[拓扑顺序迭代器](https://jgrapht.org/javadoc/org/jgrapht/traverse/TopologicalOrderIterator.html)。以上每种迭代器都对应不同的算法和不同的场景，在源码解析中会进行详细说明。  
