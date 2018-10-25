# PerformanceDemo  
``` java
import org.jgrapht.*;
import org.jgrapht.graph.*;
import org.jgrapht.traverse.*;

import java.io.*;
import java.util.*;

/**
 * 使用一张包含300万元素的图来测试CPU和内存占用情况。
 * 注意：运行这个demo前，你需要增大JVM最大内存参数。使用"-Xmx300M"将最大内存调整至300M。
 * 警告：不要在低于512M内存的电脑上运行此demo，否则电脑会卡成幻灯片。如果你非要运行，请将图中的元素数量降低，这可以通过修改下面代码中的循环次数来轻松完成。
 * @author Barak Naveh
 */
public final class PerformanceDemo
{
    /**
     * demo主入口
     * @param args ignored.
     */
    public static void main(String[] args)
    {
        long time = System.currentTimeMillis();

        reportPerformanceFor("starting at", time);

        Graph<Object, DefaultEdge> g = new Pseudograph<>(DefaultEdge.class);
        Object prev;
        Object curr;

        curr = prev = new Object();
        g.addVertex(prev);

        int numVertices = 10000;
        int numEdgesPerVertex = 200;
        int numElements = numVertices * (1 + numEdgesPerVertex);

        System.out.println(
            "\n" + "allocating graph with " + numElements
                + " elements (may take a few tens of seconds)...");

        for (int i = 0; i < numVertices; i++) {
            curr = new Object();
            g.addVertex(curr);

            for (int j = 0; j < numEdgesPerVertex; j++) {
                g.addEdge(prev, curr);
            }

            prev = curr;
        }

        reportPerformanceFor("graph allocation", time);

        time = System.currentTimeMillis();

        for (Iterator<Object> i = new BreadthFirstIterator<>(g); i.hasNext();) {
            i.next();
        }

        reportPerformanceFor("breadth traversal", time);

        time = System.currentTimeMillis();

        for (Iterator<Object> i = new DepthFirstIterator<>(g); i.hasNext();) {
            i.next();
        }

        reportPerformanceFor("depth traversal", time);

        System.out.println("\n" + "Paused: graph is still in memory (to check mem consumption).");
        System.out.print("press enter to free memory and finish...");

        try {
            System.in.read();
        } catch (IOException e) {
            e.printStackTrace();
        }

        System.out.println("done.");
    }

    private static void reportPerformanceFor(String msg, long refTime)
    {
        double time = (System.currentTimeMillis() - refTime) / 1000.0;
        double mem = usedMemory() / (1024.0 * 1024.0);
        mem = Math.round(mem * 100) / 100.0;
        System.out.println(msg + " (" + time + " sec, " + mem + "MB)");
    }

    private static long usedMemory()
    {
        Runtime rt = Runtime.getRuntime();
        return rt.totalMemory() - rt.freeMemory();
    }
}
```  
本示例为了体现JGraphT本身的算法性能，顶点和连线都选用的Object对象。测试了200万个元素的伪图构造消耗的时间和内存，以及进行广度优先和深度优先遍历时消耗的时间和内存。可以在实际的性能场景中起到一个参考作用。  
