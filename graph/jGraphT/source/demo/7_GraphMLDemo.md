# GraphMLDemo  
``` java
import org.jgrapht.*;
import org.jgrapht.generate.*;
import org.jgrapht.graph.*;
import org.jgrapht.io.*;
import org.jgrapht.io.GraphMLExporter.*;
import org.jgrapht.util.SupplierUtil;

import java.io.*;
import java.util.*;
import java.util.function.Supplier;

/**
 * 本类演示了按GraphML的格式，导入和导出包含自定义属性顶点和连线的图。图中顶点包含“color”和“name”属性，连线包含“weight”和同样的“name”属性。
 * demo中构建了一个连线权重随机的完全图并按GraphML的格式进行导出。然后将导出的结果又重新导入到第二张图中。
 */
public final class GraphMLDemo
{
    // 顶点数量
    private static final int SIZE = 6;

    // 随机数生成器
    private static final Random GENERATOR = new Random(17);

    /**
     * 颜色枚举
     */
    enum Color
    {
        BLACK("black"),
        WHITE("white");

        private final String value;

        private Color(String value)
        {
            this.value = value;
        }

        public String toString()
        {
            return value;
        }

    }

    /**
     * 自定义顶点类
     */
    static class CustomVertex
    {
        private String id;
        private Color color;

        public CustomVertex(String id)
        {
            this(id, null);
        }

        public CustomVertex(String id, Color color)
        {
            this.id = id;
            this.color = color;
        }

        @Override
        public int hashCode()
        {
            return (id == null) ? 0 : id.hashCode();
        }

        @Override
        public boolean equals(Object obj)
        {
            if (this == obj)
                return true;
            if (obj == null)
                return false;
            if (getClass() != obj.getClass())
                return false;
            CustomVertex other = (CustomVertex) obj;
            if (id == null) {
                return other.id == null;
            } else {
                return id.equals(other.id);
            }
        }

        public Color getColor()
        {
            return color;
        }

        public void setColor(Color color)
        {
            this.color = color;
        }

        public String getId()
        {
            return id;
        }

        public void setId(String id)
        {
            this.id = id;
        }

        @Override
        public String toString()
        {
            StringBuilder sb = new StringBuilder();
            sb.append("(").append(id);
            if (color != null) {
                sb.append(",").append(color);
            }
            sb.append(")");
            return sb.toString();
        }
    }

    /**
     * 创建导出器
     */
    private static GraphExporter<CustomVertex, DefaultWeightedEdge> createExporter()
    {
        /*
         * 创建顶点ID提供器，为每个顶点提供一个独特的标识符
         */
        ComponentNameProvider<CustomVertex> vertexIdProvider = v -> v.id;

        /*
         * 创建顶点标签提供器，为每个顶点提供一个（不必要唯一）的标签。如果标签值为null
         * 则导出时不会输出任何标签
         */
        ComponentNameProvider<CustomVertex> vertexLabelProvider = null;

        /*
         * 导出器需要为每个顶点生成一个属性集。属性将在下面的代码中进行注册
         */
        ComponentAttributeProvider<CustomVertex> vertexAttributeProvider = v -> {
            Map<String, Attribute> m = new HashMap<>();
            if (v.getColor() != null) {
                m.put("color", DefaultAttribute.createAttribute(v.getColor().toString()));
            }
            m.put("name", DefaultAttribute.createAttribute("node-" + v.id));
            return m;
        };

        /*
         * 创建连线ID生成器，导出器需要为每条连线生成独特的标识符
         */
        ComponentNameProvider<DefaultWeightedEdge> edgeIdProvider =
            new IntegerComponentNameProvider<>();

        /*
         * 创建连线标签生成器，导出器需要为每条连线生成（非唯一性）标签。如果标签值为null，
         * 则导出器不会导出任何标签。
         */
        ComponentNameProvider<DefaultWeightedEdge> edgeLabelProvider = null;

        /*
         * 导出器需要为每条连线生成一个属性集，所有的属性将在下面的代码中进行注册
         */
        ComponentAttributeProvider<DefaultWeightedEdge> edgeAttributeProvider = e -> {
            Map<String, Attribute> m = new HashMap<>();
            m.put("name", DefaultAttribute.createAttribute(e.toString()));
            return m;
        };

        /*
         * 创建导出器
         */
        GraphMLExporter<CustomVertex,
            DefaultWeightedEdge> exporter = new GraphMLExporter<>(
                vertexIdProvider, vertexLabelProvider, vertexAttributeProvider, edgeIdProvider,
                edgeLabelProvider, edgeAttributeProvider);

        /*
         * 设置导出时带权重
         */
        exporter.setExportEdgeWeights(true);

        /*
         * 注册顶点的颜色属性
         */
        exporter.registerAttribute("color", AttributeCategory.NODE, AttributeType.STRING);

        /*
         * 注册顶点和连线的名字属性
         */
        exporter.registerAttribute("name", AttributeCategory.ALL, AttributeType.STRING);

        return exporter;
    }

    /**
     * 创建导入器
     */
    private static GraphImporter<CustomVertex, DefaultWeightedEdge> createImporter()
    {
        /*
         * 创建顶点提供器。导入器读取顶点数据并调用顶点提供器来创建顶点模型。
         * 顶点提供器从输入流中接收每个顶点的唯一标识符和属性集。
         */
        VertexProvider<CustomVertex> vertexProvider = (id, attributes) -> {
            CustomVertex cv = new CustomVertex(id);

            // 从属性集中获取颜色属性
            if (attributes.containsKey("color")) {
                String color = attributes.get("color").getValue();
                switch (color) {
                case "black":
                    cv.setColor(Color.BLACK);
                    break;
                case "white":
                    cv.setColor(Color.WHITE);
                    break;
                default:
                    // 规避掉不支持的颜色
                }
            }

            return cv;
        };

        /*
         * 创建连线提供器。导入器从输入流中读取连线数据并调用连线提供器来创建连线模型。
         * 连线提供器从输入流中读取每条线的源点和终点、线上标签（可以为null）和属性集。
         */
        EdgeProvider<CustomVertex, DefaultWeightedEdge> edgeProvider =
            (from, to, label, attributes) -> new DefaultWeightedEdge();

        /*
         * 通过顶点和连线提供器创建导入器
         */
        GraphMLImporter<CustomVertex, DefaultWeightedEdge> importer =
            new GraphMLImporter<>(vertexProvider, edgeProvider);

        return importer;
    }

    /**
     * demo主入口
     * @param args command line arguments
     */
    public static void main(String[] args)
    {

        Supplier<CustomVertex> vSupplier = new Supplier<CustomVertex>()
        {
            private int id = 0;

            @Override
            public CustomVertex get()
            {
                return new CustomVertex(
                        String.valueOf(id++), GENERATOR.nextBoolean() ? Color.BLACK : Color.WHITE);
            }
        };

        /*
         * 生成一张完全图，顶点拥有随机的颜色，连线拥有随机的权重
         */
        Graph<CustomVertex, DefaultWeightedEdge> graph1 =
                new DirectedWeightedPseudograph<>(vSupplier, SupplierUtil.createDefaultWeightedEdgeSupplier());

        CompleteGraphGenerator<CustomVertex, DefaultWeightedEdge> completeGenerator =
            new CompleteGraphGenerator<>(SIZE);

        System.out.println("-- Generating complete graph");
        completeGenerator.generateGraph(graph1);

        /*
         * 指派随机权重
         */
        for (DefaultWeightedEdge e : graph1.edgeSet()) {
            graph1.setEdgeWeight(e, GENERATOR.nextInt(100));
        }

        try {
            // 导出，然后再导入刚刚导出的数据
            System.out.println("-- Exporting graph as GraphML");
            GraphExporter<CustomVertex, DefaultWeightedEdge> exporter = createExporter();
            // 导出为字符串
            Writer writer = new StringWriter();
            exporter.exportGraph(graph1, writer);
            String graph1AsGraphML = writer.toString();

            // 打印显示
            System.out.println(graph1AsGraphML);

            // 重新导入
            System.out.println("-- Importing graph back from GraphML");
            Graph<CustomVertex, DefaultWeightedEdge> graph2 =
                new DirectedWeightedPseudograph<>(DefaultWeightedEdge.class);
            GraphImporter<CustomVertex, DefaultWeightedEdge> importer = createImporter();
            importer.importGraph(graph2, new StringReader(graph1AsGraphML));

        } catch (ExportException | ImportException e) {
            System.err.println("Error: " + e.getMessage());
            System.exit(-1);
        }
    }
}
```  
本示例导入导出的过程，算是一个导入导出的入门教程。在[org.jgrapht.io](https://jgrapht.org/javadoc/org/jgrapht/io/package-summary.html)包中包含了多种格式的导入导出：CSV、DIMACS、DOT、GML、graph6或sparse6、GraphML、MTJ或MATLAB的格式，甚至还能导出支持导入到Visio的CSV格式。   
