# Equals和HashCode方法  
## 图的equals/hashcode方法  
从JGraphT 0.8.4版本开始，就为图形类提供了`equals(Object)`和`hashcode()`方法。  
图的`hash code`是基于图中顶点和连线的hash code之和计算得出，它还依赖于图形的拓扑结构及连线的权重。对于开发者而言，非常重要的需要了解hash code在图中的含义——“两张图相同*代表*它们拥有相同的hash code，但两张图不同*不代表*它们拥有不同的hash code”。  
两张图*相同*意味着它们是同一个图形类的实例，拥有相同的顶点和连线集合，所有连线上的权重也一模一样。注意，顶点和连线集合相同的意思是两张图中顶点和连线的对象相同。举个例子来说，如果你的顶点类没覆写equals方法，那么*同一个*顶点实例必须用在两张图中，因为在这种情况下，Java对象标识符（System.identityHashCode）会被用于顶点相同性比较，连线也是同样。  
通常来讲，图形对象的一个拷贝比如使用Graphs.addGraph产生的一个拷贝能够等同于源图。但是，如果是由源图序列化再反序列化产生的拷贝对象，除非图形中的顶点和连线对象都完全覆写equals/hashcode方法，否则拷贝对象和源对象是不等同的。  
同样需要注意的是，图形的相同性比较和同构检测是*不同*的，同构检测需要付出更高的代价。  
还有一点需要了解的是，当你使用覆写了equals/hashcode方法的自定义连线类型实现带权重的图时，在进行图形的相同性比较会产生一个exception.（如果你的自定义连线类中并没有覆写equals/hashcode方法则不用操心此问题。）下面有一个例子来展示有问题的`equals(Object)`逻辑：  
``` java
// 用户自定义带权重的连线类
class CustomWeightedEdge
	extends DefaultWeightedEdge
{
	private static final long serialVersionUID = 1L;
	private String label;

	public CustomWeightedEdge(String label)
	{
		this.label = label; 
	}

	public int hashCode()
	{
		return label.hashCode();
	}

	public boolean equals(Object obj)
	{
		if (this == obj) {
			return true;
		}
		if (obj == null) {
			return false;
		}
		if (!(obj instanceof CustomWeightedEdge)) {
			return false;
		}

		CustomWeightedEdge edge = (CustomWeightedEdge) obj;
		return label.equals(edge.label);
	}
}

// 使用自定义连线类测试图形实例
WeightedGraph<String, CustomWeightedEdge> g1 =
	new DefaultDirectedWeightedGraph<String, CustomWeightedEdge>(
	CustomWeightedEdge.class);
g1.addVertex("v1");
g1.addVertex("v2");
CustomWeightedEdge e112 = new CustomWeightedEdge("v1-v2"); 
g1.addEdge(v1, v2, e112);
g1.setEdgeWeight(e112, 10.0);

WeightedGraph<String, CustomWeightedEdge> g2 = 
	new DefaultDirectedWeightedGraph<String, CustomWeightedEdge>(
	CustomWeightedEdge.class);
g2.addVertex("v2");
g2.addVertex("v1");
CustomWeightedEdge e212 = new CustomWeightedEdge("v1-v2");
g3.addEdge(v1, v2, e212);
g3.setEdgeWeight(e212, 20.0);

g1.equals(g2); // 你预计会返回“false”，但实际上会返回“true”
```  
这种情况是`DefaultWeightedEdge`相关陷阱中的一种（其它的还包括，你不能在两张图中重用相同的DefaultWeightedEdge实例，否则两张图中的权重都会被修改）。即使不使用自定义的连线类型，这个陷阱依然存在。产生这个问题的主要原因是由于`DefaultWeightedEdge`在内部存储了连线的权重并且`AbstractGraph.getEdgeWeight(E)`方法仅提供了基于DefaultWeightedEdge的权重检索。所以在使用`equals(Object)`方法进行两条相同连线的权重比较时，实际上比较的是同一条连线的权重。  
所以如果你想要图形的相同性比较方法正确运行，要么你的自定义连线类中别去覆写`equals/hashCode`方法，要么你别去继承DefaultWeightedEdge。  
## 顶点/连线对象的equals/hashCode方法  
在网上有很多文章（比如[这篇](http://www.ibm.com/developerworks/java/library/j-jtp05273.html)）讲述为什么每个类都需要实现equals和hashCode方法，以及一些其它重要的原则比如不要把可变的类做为key等等。  
而这点对于JGraphT中顶点和连线尤其重要，因为默认的图实现类依赖于HashSet/HashMap结构来存储及访问图形结构。  
如果你在equals/hashCode方法上犯了错误，那么对图形的观察及访问通常会出现一些让人迷惑的结果。在很多场景下，很多你认为是JGraphT导致的bug实际上是由于自定义的顶点/连线类产生的。  
比如说，假设你的顶点类中覆写了equals方法，但忘了覆写hashCode方法。你增加了5个顶点v1,v2,v3,v4,v5，其中v3.equals(v5)，然后你将它们添加到一张图中，连线的方式是v1→v2→v3→v4→v5。如果hashCode方法定义正确，那么v5的添加操作会被忽略，因为有一个相同的顶点（v3）已经被添加到图中了。  
但是，没覆写hashCode方法时，v3和v5的hashCode是不同的（因为它们是不同的对象，默认hashCode方法是基于对象标识符实现的）。在把它们加入到图中时，会将这两个实例放置到一个数组中由图实例内的HashMap来调用，它们的hashcode不会产生冲突。结果就是，当你添加v5到图中时，HashMap根本不会将v5和v3进行相同性比较，因为仅仅在hashcode产生冲突时，它才会调用equals方法进行判断。于是，你就获得了一个损坏的图，因为它拥有两个等同的顶点对象，这样就违反了图的约定。  
在这种情况下，你有可能一开始不会看到任何错误。但是，一旦你在图上使用CycleDetector类，就能得到一个惊喜了，因为它依赖于ArrayList.indexOf方法，而这个方法不会执行任何hash操作，于是它判断v3和v5是等同的，并在图中找到一个环（实际上你认为v3和v5是不同的顶点）。  
追踪这样的错误可能会耗时良久，因此JGraphT提供了一个ParanoidGraph类来帮助你。你可以使用它来包装你的图；通过调用ParanoidGraph上的addVertext/addEdge方法（而不是直接在你的源图上调用），就能在添加图形元素时自动检测对象的合理性；如果侦测到存在坏对象，则会抛出一个IllegalArgumentException。注意这个检测非常消耗性能，所以仅在调试或健全检查中使用它。  
下面是一个单元测试的例子：  
``` java
	public void testParanoidGraph()
	{
	   BrokenVertex v1 = new BrokenVertex(1);
	   BrokenVertex v2 = new BrokenVertex(2);
	   BrokenVertex v3 = new BrokenVertex(1);

	   SimpleGraph<BrokenVertex, DefaultEdge> g =
	       new SimpleGraph<BrokenVertex, DefaultEdge>(DefaultEdge.class);
	    ParanoidGraph<BrokenVertex, DefaultEdge> pg =
	       new ParanoidGraph<BrokenVertex, DefaultEdge>(g);
	   pg.addVertex(v1);
	   pg.addVertex(v2);
	   try {
	       pg.addVertex(v3);
	       // 应该不会运行到下面这句
	       assertFalse();
	   } catch (IllegalArgumentException ex) {
	       // 预计会抛出异常，跳至此处
	   }
	}

	private class BrokenVertex
	{
	   private int x;

	   BrokenVertex(int x)
	   {
	       this.x = x;
	   }

	   public boolean equals(Object other)
	   {
	       if (!(other instanceof BrokenVertex)) {
	           return false;
	       }
	       return x == ((BrokenVertex) other).x;
	   }
	}
```  

[返回目录](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/apply/dev#jgrapht%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97%E6%80%BB%E7%BA%B2)
