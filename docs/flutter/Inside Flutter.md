（对官方文档的翻译记录[https://flutter.dev/docs/resources/inside-flutter](https://flutter.dev/docs/resources/inside-flutter)）

本文档介绍了Flutter工具箱的内部工作原理，这些工具使Flutter的API成为可能。 由于Flutter小部件是使用积极的构图构建的，因此使用Flutter构建的用户界面具有大量小部件。 为了支持此工作量，Flutter使用亚线性算法进行布局和构建小部件以及数据结构，这些方法使树木手术更加高效并且具有许多恒定因子优化。 通过一些其他细节，此设计还使开发人员可以轻松使用回调构建无限滚动列表，这些回调将完全构建用户可见的那些小部件。

## Aggressive composability（可组合性）

One of the most distinctive aspects of Flutter is its aggressive composability. Widgets are built by composing other widgets, which are themselves built out of progressively more basic widgets. For example, Padding is a widget rather than a property of other widgets. As a result, user interfaces built with Flutter consist of many, many widgets.

Flutter最具特色的方面之一是其积极的可组合性。 窗口小部件是通过组合其他窗口小部件而构建的，其他窗口小部件本身是由逐渐更基本的窗口小部件构建而成的。 例如，填充是窗口小部件，而不是其他窗口小部件的属性。 结果，使用Flutter构建的用户界面包含许多小部件。

The widget building recursion bottoms out in **RenderObjectWidgets**, which are widgets that create nodes in the underlying render tree. The render tree is a data structure that stores the **geometry** of the user interface, which is computed during layout and used during painting and hit testing. Most Flutter developers do not author render objects directly but instead manipulate the render tree using widgets.

构建递归的小部件是从**RenderObjectWidgets**开始的，这些小部件在底层呈现树中创建节点。渲染树是存储用户界面几何图形的数据结构，它在布局期间计算，并在绘制和命中测试期间使用。大多数Flutter开发人员并不直接编写渲染对象，而是使用小部件操作渲染树。

In order to support aggressive composability at the widget layer, Flutter uses a number of efficient algorithms and optimizations at both the widget and render tree layers, which are described in the following subsections.

为了在小部件层支持积极的可组合性，Flutter在小部件层和渲染树层都使用了许多有效的算法和优化方法，这些将在以下小节中进行介绍。

## Sublinear layout（线性布局）

With a large number of widgets and render objects, the key to good performance is efficient algorithms. Of paramount importance is the performance of layout, which is the algorithm that determines the geometry (for example, the size and position) of the render objects. Some other toolkits use layout algorithms that are O(N²) or worse (for example, fixed-point iteration in some constraint domain). Flutter aims for linear performance for initial layout, and sublinear layout performance in the common case of subsequently updating an existing layout. Typically, the amount of time spent in layout should scale more slowly than the number of render objects.

对于大量的小部件和渲染对象，高效的算法是获得良好性能的关键。 布局的性能是最重要的，布局的性能是确定渲染对象的几何形状（例如，大小和位置）的算法。 其他一些工具箱使用的是O（N²）或更差的布局算法（例如，某些约束域中的定点迭代）。 Flutter旨在为初始布局提供线性性能，并在随后更新现有布局的常见情况下实现亚线性布局性能。 通常，布局所花费的时间比例应比渲染对象的数量更慢。

Flutter performs one layout per frame, and the layout algorithm works in a single pass. Constraints are passed down the tree by parent objects calling the layout method on each of their children. The children recursively perform their own layout and then return geometry up the tree by returning from their layout method. Importantly, once a render object has returned from its layout method, that render object will not be visited again1 until the layout for the next frame. This approach combines what might otherwise be separate measure and layout passes into a single pass and, as a result,  each render object is visited *at most twice**2 *during layout: once on the way down the tree, and once on the way up the tree.

flutter每帧执行一次布局，布局算法工作在一次。约束由父对象在树中传递，父对象调用其子对象的布局方法。子节点递归地执行它们自己的布局，然后通过从它们的布局方法返回树中的几何图形。**重要的是，一旦渲染对象从它的布局方法返回，渲染对象将不会被再次访问1，直到布局为下一帧**。**这种方法将原本可能是单独的度量和布局传递组合为单个传递，结果是，****在布局过程中，每个渲染对象最多被访问两次:一次在树的下行过程中，一次在树的上行过程中。**

Flutter has several specializations of this general protocol. The most common specialization is `RenderBox`, which operates in two-dimensional, cartesian coordinates. In box layout, the constraints are a **min and max width and a min and max height**. During layout, the child determines its geometry by choosing a size within these bounds. After the child returns from layout, the parent decides the child’s position in the parent’s coordinate system3. **Note that the child’s layout cannot depend on its position, as the position is not determined until after the child returns from the layout**. As a result, the parent is free to reposition the child without needing to recompute its layout. 

Flutter对这个通用的规范化。最常见的规范化的是RenderBox，它在二维笛卡尔坐标中运行。在框布局中，限制是**最小和最大宽度和最小和最大高度**。在布局过程中，子元素通过在这些范围内选择大小来确定其几何形状。当子元素从布局中返回后，父元素决定子元素在父元素**坐标系统中的位置。注意，子元素的布局不能依赖于它的位置，因为直到子元素从布局返回后，位置才确定。**因此，父节点可以自由地重新定位子节点，而不需要重新计算其子节点的布局。

More generally, during layout, the *only* information that flows from parent to child are the **constraints** and the *only* information that flows from child to parent is the **geometry**. These invariants can reduce the amount of work required during layout:

更一般地，在布局过程中，惟一从父元素流向子元素的信息是约束，惟一从子元素流向父元素的信息是几何图形。这些不变量可以减少布局过程中所需的工作量：

If the child has not marked its own layout as dirty, the child can return immediately from layout, cutting off the walk, as long as the parent gives the child the same constraints as the child received during the previous layout.

如果child没有将自己的布局标记为dirty，则只要父母给孩子与上一次布局中收到的child相同的约束，孩子就可以立即从布局中返回并切断步行。

Whenever a parent calls a child’s layout method, the parent indicates whether it uses the size information returned from the child. If, as often happens, the parent does not use the size information, then the parent need not recompute its layout if the child selects a new size because the parent is guaranteed that the new size will conform to the existing constraints.

每当父元素调用子元素的布局方法时，父元素就指示它是否使用子元素返回的大小信息。如果父元素通常不使用大小信息，那么如果子元素选择了新的大小，父元素就不需要重新计算其布局，因为父元素保证了新的大小将符合现有的约束。

*Tight* constraints are those that can be satisfied by exactly one valid geometry. For example,** if the min and max widths are equal to each other and the min and max heights are equal to each other, the only size that satisfies those constraints is one with that width and height**. If the parent provides tight constraints, then the parent need not recompute its layout whenever the child recomputes its layout, even if the parent uses the child’s size in its layout, because the child cannot change size without new constraints from its parent.

严格约束是可以仅通过一种有效几何形状满足的约束。 例如，**如果最小和最大宽度彼此相等，而最小和最大高度彼此相等，则满足这些约束的唯一尺寸就是具有该宽度和高度的尺寸**。 如果父级提供严格的约束，则即**使子级重新计算其布局，父级也无需重新计算其布局**，即使父级在其布局中使用了子级的尺寸，因为如果没有父级的新约束，子级也无法更改尺寸。







