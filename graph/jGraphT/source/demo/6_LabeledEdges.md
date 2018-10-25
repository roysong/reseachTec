# LabeledEdges  
``` java
import org.jgrapht.*;
import org.jgrapht.graph.*;

import java.util.*;

/**
 * 采用自定义连线类实现带标签的连线
 * @author Barak Naveh
 */
public class LabeledEdges
{
    private static final String FRIEND = "friend";
    private static final String ENEMY = "enemy";

    /**
     * demo主入口
     * @param args ignored.
     */
    public static void main(String[] args)
    {
        Graph<String, RelationshipEdge> graph = new DefaultDirectedGraph<>(RelationshipEdge.class);

        ArrayList<String> people = new ArrayList<String>();
        people.add("John");
        people.add("James");
        people.add("Sarah");
        people.add("Jessica");

        // John是每个人的朋友
        for (String person : people) {
            graph.addVertex(person);
            if (!person.equals("John")) {
                graph.addEdge(
                    "John", person,
                    new RelationshipEdge(FRIEND));
            }
        }

        // 很明显James真的不喜欢John
        graph.addEdge("James", "John", new RelationshipEdge(ENEMY));

        // Jessica是Sarah和James的朋友
        graph.addEdge("Jessica", "Sarah", new RelationshipEdge(FRIEND));
        graph.addEdge("Jessica", "James", new RelationshipEdge(FRIEND));

        // 但Sarah真的不喜欢James
        graph.addEdge("Sarah", "James", new RelationshipEdge(ENEMY));

        for (RelationshipEdge edge : graph.edgeSet()) {
            String v1 = graph.getEdgeSource(edge);
            String v2 = graph.getEdgeTarget(edge);
            if (edge.getLabel().equals("enemy")) {
                System.out.printf(v1 + " is an enemy of " + v2 + "\n");
            } else if (edge.getLabel().equals("friend")) {
                System.out.printf(v1 + " is a friend of " + v2 + "\n");
            }
        }
        assert(isEnemyOf(graph, "James", "John"));
    }

    private static boolean isEnemyOf(
        Graph<String, RelationshipEdge> graph,
        String person1,
        String person2)
    {
        return graph.getEdge(person1, person2).getLabel().equals(ENEMY);
    }
}

/**
 * 以关系类型为标签的连线
 */
class RelationshipEdge extends DefaultEdge
{
    private String label;

    /**
     * 使用标签的构造方法
     * @param label the label of the new edge.
     * 
     */
    public RelationshipEdge(String label)
    {
        this.label = label;
    }

    /**
     * 获取线上的标签
     * @return edge label
     */
    public String getLabel()
    {
        return label;
    }

    @Override
    public String toString()
    {
        return "(" + getSource() + " : " + getTarget() + " : " + label + ")";
    }
}
```  
本示例的最大价值就是演示了如何以自定义类型扩展连线的功能，功能简单，演示清晰，直接阅读代码即可。  
