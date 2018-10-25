# GraphBuilderDemo  
``` java
import org.jgrapht.*;
import org.jgrapht.graph.*;
import org.jgrapht.graph.builder.*;

/**
 * 演示如何使用GraphBuilder来构建图
 * @author John Sichi
 */
public final class GraphBuilderDemo
{
    /**
     * demo入口
     * 
     * @param args command line arguments
     */
    public static void main(String [] args)
    {
        Graph<Integer, DefaultEdge> kite = buildKiteGraph();
    }

    /**
     * 不使用任何顶点构建一张简单图
     *
     * @return 可修改的空图实例
     */
    private static Graph<Integer, DefaultEdge> buildEmptySimpleGraph()
    {
        return GraphTypeBuilder.<Integer, DefaultEdge>undirected().
            allowingMultipleEdges(false).allowingSelfLoops(false).
            edgeClass(DefaultEdge.class).weighted(false).buildGraph();
    }

    /**
     * 构建一张风筝图（KiteGraph）
     *
     * @return 一张只读的风筝图实例
     */
    private static Graph<Integer, DefaultEdge> buildKiteGraph()
    {
        return new GraphBuilder<>(buildEmptySimpleGraph()).
            addEdgeChain(1, 2, 3, 4, 1).
            addEdge(2, 4).
            addEdge(3, 5).buildAsUnmodifiable();
    }
}
```  
本示例演示的是通过参数实例化图的方法，而不是直接实例化预设类型图。  
这个例子本身简单易懂、乏善可陈，但里面有个概念是之前没有见过的——风筝图（KiteGraph），开始我以为会是特别复杂的模型，然而它是这个样子的：  

![风筝图](http://mathworld.wolfram.com/images/eps-gif/KiteGraph_1000.gif)

然后我觉得，这个很简单嘛！然后看到了风筝图的定义：  
> 若有一个图，其顶点数为偶数（比如2n），且其中的n个顶点构成一个团，其余的n个顶点则有一条称为“尾巴”的路径连接，尾巴的某个端点与团中一个顶点相连，这样的图称为“风筝图”。  ----《[算法概论](http://dx5.wishdown.com:16808/soft/suanfagailun_wishdown.zip)》

![emmmmmm](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1540891627&di=80cd7aedfbe5d809760d1b6d838c752a&imgtype=jpg&er=1&src=http%3A%2F%2Fuploads.xuexila.com%2Fallimg%2F1708%2F856-1FS0104408.jpg)


