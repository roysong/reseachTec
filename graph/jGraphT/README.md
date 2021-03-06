# JGraphT 项目研究
## JGraphT简介
JGraphT是一个开源的JAVA图论项目，提供了基于数理的图论对象及算法。它支持多种类型的图形：  
* 有向图和无向图  
* 加权/无权/标签连线或者用户自定义的任意连线  
* 支持多种连线属性图：简单图（simple-graphs）、多重图（ multigraphs）和伪图（pseudographs）  
* 支持不可变图--允许将图设置为“只读”  
* 支持可监听图--允许外部监听器追踪图上的改变事件  
* 支持子图--在其它图上能够自动更新的图层子区域  
* 支持以上所有图的随意组合  

尽管功能强大，JGraphT设计得简约且类型安全（通过Java泛型）。比如，图中的顶点（vertices）能够支持任意对象类型。你可以采用字符串、超链接、XML文档等等做为图的顶点，你甚至可以将一整张图作为另一张图的顶点。  
JGraphT还提供以下功能：  
* 采用JGraphX来实现可视化  
* 基于GPL和EPL开源协议开放所有源码  
* [综合型API文档](https://jgrapht.org/javadoc)    
* 良好的可扩展性   
## JGraphT和JGraphX的关系
JGraphT和JGraphX是基于计算机图形学针对方向不同的两个开源库：  
JGraphT专注于图论中的数据结构和算法，而JGraphX专注于图形的渲染及图形的编辑。  
这两个开源库能够相互补充，通过JGraphT提供的JGraphXAdapter能够让两个开源库联合使用，将JGraphT中的数据结构转换为JGraphX中的模型，应用于图形渲染，并由JGraphX进行模型控制。  
## 为什么开展JGraphT的研究  
1. 从产品战略的角度来讲，可视化是我们产品的四架马车之一。产品的可视化部分不仅要为客户提供可观看的业务图形，还要为客户提供可交互的业务图形。在图形的交互上，也会由简单的点击、拖动等操作逐步演化为图上的辅助计算，如拓扑的自动布局、电路设计等。所以需要对图论的应用进行深入研究。  
2. 从业务的角度来讲，随着IT的不断发展，用户也在不断演变，对系统易用性的要求也在不断提升。未来系统的操作必然向简单化、直观化方向发展。用户会更倾向于在直观的图形上进行业务操作，而不是传统的表单或者列表上操作，来完成相应的业务。所以，需要对图形操作的应用场景进行研究。  
3. 从技术角度来讲，计算机图形学涉及数据结构和算法，能够加深研发人员对技术本质的理解，提升技术水平。  

基于以上三点理由，由此开展对JGraphT的研究。  
## 研究方式  
由于语言壁垒（全英文资料）和技术壁垒（图论所要求的数学基础及算法）的缘故，国内技术社区对JGraphT的原理及应用几乎没有公开的资料。  
因此，我们对JGraphT的研究分成两部分，源码研究和应用研究。源码研究的相关资料放在source目录下，应用研究的相关资料放在apply目录下。  
## 学习路线  
首先阅读[开发指南](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/apply/dev#jgrapht%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97%E6%80%BB%E7%BA%B2)，对JGraphT建立起功能上的整体概念；然后进行[demo学习](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/source/demo#%E6%BA%90%E7%A0%81demo%E5%AD%A6%E4%B9%A0),了解JGraphT在开发中的具体使用方式；再进行[核心源码学习](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/source/core)，了解其实现机制；最后了解其[使用场景](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/apply/case)和[应用技术总结](https://github.com/roysong/reseachTec/tree/master/graph/jGraphT/apply/use)。  
