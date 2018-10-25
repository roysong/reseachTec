# HelloJGraphT
``` java
package org.jgrapht.demo;

import org.jgrapht.*;
import org.jgrapht.graph.*;
import org.jgrapht.traverse.*;
import org.jgrapht.io.*;
import java.net.*;
import java.util.*;
import java.io.*;

/**
 * 对JGraphT使用的简单介绍
 * @author Barak Naveh
 */
public final class HelloJGraphT
{
    private HelloJGraphT()
    {
    } // 确保无法实例化

    /**
     * demo的起始点
     * @param args ignored.
     * @throws MalformedURLException if invalid URL is constructed.
     * @throws ExportException if graph cannot be exported.
     */
    public static void main(String[] args)
        throws MalformedURLException, ExportException
    {
        Graph<String, DefaultEdge> stringGraph = createStringGraph();
        // 注意无向图的连线会被打印为: {<v1>,<v2>}
        System.out.println("-- toString output");
        System.out.println(stringGraph.toString());
        System.out.println();
        // 基于URL对象构建图
        Graph<URL, DefaultEdge> hrefGraph = createHrefGraph();
        // 根据超链接www.jgrapht.org找到对应的顶点对象
        URL start = hrefGraph.vertexSet().stream().filter(
            url -> url.getHost().equals("www.jgrapht.org")).findAny().get();
        // 从固定顶点开始遍历全图
        System.out.println("-- traverseHrefGraph output");
        traverseHrefGraph(hrefGraph, start);
        System.out.println();

        System.out.println("-- renderHrefGraph output");
        renderHrefGraph(hrefGraph);
        System.out.println();
    }

    /**
     * 使用代表连接结构的URL对象构建一个简单有向图
     * @return a graph based on URL objects.
     */
    private static Graph<URL, DefaultEdge> createHrefGraph()
        throws MalformedURLException
    {
        Graph<URL, DefaultEdge> g = new DefaultDirectedGraph<>(DefaultEdge.class);
        URL google = new URL("http://www.google.com");
        URL wikipedia = new URL("http://www.wikipedia.org");
        URL jgrapht = new URL("http://www.jgrapht.org");
        // 添加顶点
        g.addVertex(google);
        g.addVertex(wikipedia);
        g.addVertex(jgrapht);
        // 添加连线以建立连接结构
        g.addEdge(jgrapht, wikipedia);
        g.addEdge(google, jgrapht);
        g.addEdge(google, wikipedia);
        g.addEdge(wikipedia, google);
        return g;
    }

    /**
     * 以深度优先顺序对图进行遍历并打印出顶点来
     * @param hrefGraph a graph based on URL objects
     * @param start the vertex where the traversal should start
     */
    private static void traverseHrefGraph(Graph<URL, DefaultEdge> hrefGraph, URL start)
    {
        Iterator<URL> iterator = new DepthFirstIterator<>(hrefGraph, start);
        while (iterator.hasNext()) {
            URL url = iterator.next();
            System.out.println(url);
        }
    }

    /**
     * 按DOT格式转换图形数据
     * @param hrefGraph a graph based on URL objects
     */
    private static void renderHrefGraph(Graph<URL, DefaultEdge> hrefGraph)
        throws ExportException
    {
        // 使用帮助类来定义顶点如何按DOT格式进行渲染
        ComponentNameProvider<URL> vertexIdProvider =
            new ComponentNameProvider<URL>()
            {
                public String getName(URL url)
                {
                    return url.getHost().replace('.', '_');
                }
            };
        ComponentNameProvider<URL> vertexLabelProvider =
            new ComponentNameProvider<URL>()
            {
                public String getName(URL url)
                {
                    return url.toString();
                }
            };
        GraphExporter<URL, DefaultEdge> exporter = new DOTExporter<>(
            vertexIdProvider, vertexLabelProvider, null);
        Writer writer = new StringWriter();
        exporter.exportGraph(hrefGraph, writer);
        System.out.println(writer.toString());
    }
    
    /**
     * 基于String对象创建一个示例图
     * @return a graph based on String objects.
     */
    private static Graph<String, DefaultEdge> createStringGraph()
    {
        Graph<String, DefaultEdge> g = new SimpleGraph<>(DefaultEdge.class);

        String v1 = "v1";
        String v2 = "v2";
        String v3 = "v3";
        String v4 = "v4";

        // 添加所有顶点
        g.addVertex(v1);
        g.addVertex(v2);
        g.addVertex(v3);
        g.addVertex(v4);

        // 添加连线创建一个环形图
        g.addEdge(v1, v2);
        g.addEdge(v2, v3);
        g.addEdge(v3, v4);
        g.addEdge(v4, v1);

        return g;
    }
}
```  
本示例主要是针对[开发指南](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/apply/dev#jgrapht%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97%E6%80%BB%E7%BA%B2)中的内容进行的实例演示。  
包含了构建简单图、遍历图和按GraphViz.Dot格式导出的功能。  
这个示例做为开发指南的配套样例，整体来说比较简单，覆盖不够全面，技术的深入程度也不足。只能起到入门初步理解概念的作用。  
