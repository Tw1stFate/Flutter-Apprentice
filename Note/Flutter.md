# Flutter

![Flutter架构图](https://static001.geekbang.org/resource/image/ac/2f/ac7d1cec200f7ea7cb6cbab04eda252f.png)

## Widget
Flutter核心设计思想是"一切皆是Widget", Widget是flutter功能的抽象描述, 是视图的配置信息, 也是数据的映射. 

Widget渲染过程:  Flutter会结构化地组织视图数据, 提供渲染引擎完成界面显示. 视图的组织和渲染被抽象为三部分.
   * `Widget`: Widget是对视图的一种结构化描述，是控件实现的基本逻辑单位，里面存储的是有关视图渲染的配置信息，包括布局、渲染属性、事件响应信息等。Widget被设计为**不可变**, 当视图渲染的配置信息发生变化时, flutter会选择重新构建Widget树的方式进行数据更新, 以数据驱动UI更新. 缺点是，因为涉及到大量对象的销毁和重建，所以会对垃圾回收造成压力。不过，Widget 本身并不涉及实际渲染位图，所以它只是一份轻量级的数据结构，重建的成本很低。
   * `Element`: Element是Widget的一个**可变的**实例化对象，它承载了视图构建的上下文数据，是连接结构化的配置信息到完成最终渲染的桥梁。 **Element 树这一层将 `Widget` 树的变化（类似 React 虚拟 DOM diff）做了抽象，可以只将真正需要修改的部分同步到真实的 RenderObject 树中，最大程度降低对真实渲染视图的修改，提高渲染效率，而不是销毁整个渲染视图树重建。**
   * `RenderObject`: 包含视图渲染信息的对象. 渲染对象树在 Flutter 的展示过程分为四个阶段，即布局、绘制、合成和渲染。 其中，布局和绘制在 RenderObject 中完成，Flutter 采用深度优先机制遍历渲染对象树，确定树中各个对象的位置和尺寸，并把它们绘制到不同的图层上。绘制完毕后，合成和渲染的工作则交给 Skia 搞定。
4333
渲染过程可分为三步:
1. 通过`Widget`树生成对应的`Element`树.
2. 创建相应的 `RenderObject` 并关联到 `Element.renderObject` 属性上.
3. 构建成 `RenderObject` 树，以完成最终的渲染。