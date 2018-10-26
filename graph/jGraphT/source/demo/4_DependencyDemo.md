# DependencyDemo  
``` java
import org.jgrapht.*;
import org.jgrapht.alg.cycle.CycleDetector;
import org.jgrapht.graph.*;
import org.jgrapht.traverse.*;

import java.util.*;

/***
 * 本类演示了如何创建一个带依赖形的有向图，然后提供了定位并输出图中所有能产生无限循环的环状结构
 **/
public class DependencyDemo
{

    /**
     * 创建一张有向图，检查其中是否具备环状结构，如果有，则输出每个环包含的顶点；如果没有，
     * 则输出逻辑上的拓扑结构
     * @param createCycles true - 创建带环状结构的有向图. false - 创建不带任何环的有向图
     */
    public static void test(boolean createCycles)
    {
        CycleDetector<String, DefaultEdge> cycleDetector;
        Graph<String, DefaultEdge> g;

        g = new DefaultDirectedGraph<String, DefaultEdge>(DefaultEdge.class);

        // 添加顶点
        g.addVertex("a");
        g.addVertex("b");
        g.addVertex("c");
        g.addVertex("d");
        g.addVertex("e");

        // 添加连线

        g.addEdge("b", "a");
        g.addEdge("c", "b");
        if (createCycles) {
            g.addEdge("a", "c");
        }
        g.addEdge("e", "d");
        if (createCycles) {
            g.addEdge("d", "e");
        }

        // 打印所有顶点和连线
        System.out.println(g.toString());

        // 创建用于检查是否存在环的探测器
        cycleDetector = new CycleDetector<String, DefaultEdge>(g);

        // 检查是否成环
        if (cycleDetector.detectCycles()) {
            Iterator<String> iterator;
            Set<String> cycleVertices;
            Set<String> subCycle;
            String cycle;

            System.out.println("Cycles detected.");

            // 获取环中所有的顶点
            cycleVertices = cycleDetector.findCycles();

            // 循环环中顶点来找出不相交的环
            while (!cycleVertices.isEmpty()) {
                System.out.println("Cycle:");

                // 获取环中一个顶点
                iterator = cycleVertices.iterator();
                cycle = iterator.next();

                // 获取与此顶点相关联的所有顶点
                subCycle = cycleDetector.findCyclesContainingVertex(cycle);
                for (String sub : subCycle) {
                    System.out.println("   " + sub);
                    // 移除相关顶点以免对同一环重复计数
                    cycleVertices.remove(sub);
                }
            }
        }

        // 如果没有环状结构，则按拓扑顺序输出所有顶点
        else {
            String v;
            TopologicalOrderIterator<String, DefaultEdge> orderIterator;

            orderIterator = new TopologicalOrderIterator<String, DefaultEdge>(g);
            System.out.println("\nTopological Ordering:");
            while (orderIterator.hasNext()) {
                v = orderIterator.next();
                System.out.println(v);
            }
        }
    }

    /**
     * 创建两个用例，第一个带有环状结构即具备依赖性，第二个则没有
     * 
     * @param args Ignored.
     */
    public static void main(String[] args)
    {
        System.out.println("\nCase 1: There are cycles.");
        test(true);

        System.out.println("\nCase 2: There are no cycles.");
        test(false);

        System.out.println("\nAll done");
        System.exit(0);
    }
}
```  
这个示例并非开发指南中的样例，并且针对图形中一个常见场景——如何检测图中的环状结构——进行了使用演示，非常具有代表含义。  
而且，这个示例本身只是一个[入口](https://jgrapht.org/javadoc/org/jgrapht/alg/cycle/package-summary.html),JGraphT中针对环的检测提供了一系列的算法，分为三类： 
## 简单环的算法  
首先需要理解，什么是[简单环（simple cycle）](https://en.wikipedia.org/wiki/Cycle_(graph_theory))？  
在[图论](https://en.wikipedia.org/wiki/Graph_theory)中，顶点和连线依次相连即构成*路（walk）*，开始顶点和结束顶点为同一顶点的路被称为*环（cycle）*。如果一条路中不包含重复顶点和重复连线，那么这条路被称为*简单路（simple walk）*；而开始顶点和结束顶点相同的简单路就被称为*简单环*。  
JGraphT的cycle包中包含了四种不同算法来辨别有向图中的简单环。四种算法的最差时间复杂度如下：  
1. Szwarcfiter and Lauer - O(V+EC)  
2. Tarjan - O(VEC)  
3. Johnson - O(((V+E)C) 
4. Tiernan - O(V.const的V次方)  

上面的*V*代表了顶点的数量，*E*代表连线的数量，*C*则是图中简单环的数量。上面四种算法均能支持包含自循环的图，但不能支持多重图。这四种算法的空间复杂度是*O(V+E)*。  
由于特殊结构的图本身便达到了最高的性能复杂度，所以，在实际运行过程中，高复杂度算法有可能比低复杂度算法性能更优。同样需要注意的是，某些复杂度较低的算法耗费的“调度开销”会更大，占用内存也会更多。  
上述算法的细节可以在以下论文中查阅：  
> 1. J.C.Tiernan An Efficient Search Algorithm Find the Elementary Circuits of a Graph., Communications of the ACM, V13, 12, (1970), pp. 722 - 726.  
> 2. R.Tarjan, Depth-first search and linear graph algorithms., SIAM J. Comput. 1 (1972), pp. 146-160.  
> 3. R. Tarjan, Enumeration of the elementary circuits of a directed graph, SIAM J. Comput., 2 (1973), pp. 211-216.  
> 4. D. B. Johnson, Finding all the elementary circuits of a directed graph, SIAM J. Comput., 4 (1975), pp. 77-84.  
> 5. J. L. Szwarcfiter and P. E. Lauer, Finding the elementary cycles of a directed graph in O(n + m) per cycle, Technical Report Series, #60, May 1974, Univ. of Newcastle upon Tyne, Newcastle upon Tyne, England.  
> 6. L. G. Bezem and J. van Leeuwen, Enumeration in graphs., Technical report RUU-CS-87-7, University of Utrecht, The Netherlands, 1987.  

## 无向基础环（[cycle basis](https://en.wikipedia.org/wiki/Cycle_basis)）的计算算法  
1. Paton算法的一个变种[PatonCycleBase](https://jgrapht.org/javadoc/org/jgrapht/alg/cycle/PatonCycleBase.html)，使用栈执行一个BFS（广度优先搜索），返回一个弱连接[基本基础环](https://en.wikipedia.org/wiki/Cycle_basis#Fundamental_cycles)。支持自循环图，但不支持多重（并发）连线。  
2. Paton算法的另一个变种[StackBFSFundamentalCycleBasis](https://jgrapht.org/javadoc/org/jgrapht/alg/cycle/StackBFSFundamentalCycleBasis.html)，返回一个基本基础环。这是一个更通用的实现，能够同时支持自循环和多重（并发）连线。  
3. 算法[QueueBFSFundamentalCycleBasis](https://jgrapht.org/javadoc/org/jgrapht/alg/cycle/QueueBFSFundamentalCycleBasis.html)采用队列结构直接实现了广度优先搜索，构建出一个基本基础环。这种实现也同时支持自循环和多重（并发）连线。  

上述三种算法的最差时间复杂度为O(|V|的3次方)，因为基础环的大小就可以有那么大。  
上述算法的细节可以在以下论文中查看：  
> 1. K. Paton, An algorithm for finding a fundamental set of cycles for an undirected linear graph, Comm. ACM 12 (1969), pp. 514-518.  
> 2. Narsingh Deo, G. Prabhu, and M. S. Krishnamoorthy. Algorithms for Generating Fundamental Cycles in a Graph. ACM Trans. Math. Softw. 8, 1, 26-42, 1982.  

## [欧拉回路](https://www.jianshu.com/p/e7524d6ab6ca)的计算算法  
1. 一个Hierholzer算法的[实现](https://jgrapht.org/javadoc/org/jgrapht/alg/cycle/HierholzerEulerianCycle.html)用于计算欧拉图中的欧拉回路。  


