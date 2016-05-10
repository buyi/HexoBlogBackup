title: RecyclerView示例
date: 2015-11-12 10:12:19
tags:
---
##RecyclerView学习

A flexible view for providing a limited window into a large data set.

RecyclerView是一个非常灵活的在有限屏幕上显示大量数据集的视图控件。

###Glossary of terms:
一些术语解释

Adapter: A subclass of RecyclerView.Adapter responsible for providing views that represent items in a data set. 

适配器:Adapter的责任就是从数据集合中返回要呈现的item view。

Position: The position of a data item within an Adapter.

数据在Adapter中的数据集合中的位置

Index: The index of an attached child view as used in a call to getChildAt(int). Contrast with Position.

相对于Position而言，它是加在child view中的索引

 Binding: The process of preparing a child view to display data corresponding to a position within the adapter.
 
 绑定实际上就是准备一个子view来显示adapter中对应位置的数据的过程
 
Recycle (view): A view previously used to display data for a specific adapter position may be placed in a cache for later reuse to display the same type of data again later. This can drastically improve performance by skipping initial layout inflation or construction.

一个之前用来显示指定adapter位置的数据的视图可能会保存在缓存中，在未来的某个时刻会复用来显示相同类型的数据。这种策略可以大幅度地提升性能，因为它省略了初始化布局导入和构造的时间

 Scrap (view): A child view that has entered into a temporarily detached state during layout. Scrap views may be reused without becoming fully detached from the parent RecyclerView, either unmodified if no rebinding is required or modified by the adapter if the view was considered dirty.
 
 碎片视图。一个子view会在布局时短暂进入detached状态。碎片view可以被复用而不需要完全脱离父RecyclerView，无论是 不重绑定无需修改 还是view被认为是dirty view而需要修改
 
 
Dirty (view): A child view that must be rebound by the adapter before being displayed.

子view必须在显示前进行重绑定。

Positions in RecyclerView:

RecyclerView introduces an additional level of abstraction between the RecyclerView.Adapter and RecyclerView.LayoutManager to be able to detect data set changes in batches during a layout calculation. This saves LayoutManager from tracking adapter changes to calculate animations. It also helps with performance because all view bindings happen at the same time and unnecessary bindings are avoided.

RecyclerView介绍了一个在adapter和layoutmanager之间的额外的抽象水平，这样的话在布局计算的过程中可以探查到数据集合的变化。保存一份LayoutManager是为了能够跟踪adapter的变化来进一步计算动画。这个同样可以提升性能因为所有的view绑定发生在同一时间，并且避免了不必要的绑定。

For this reason, there are two types of position related methods in RecyclerView:

- layout position: Position of an item in the latest layout calculation. This is the position from the LayoutManager's perspective.
- adapter position: Position of an item in the adapter. This is the position from the Adapter's perspective.

These two positions are the same except the time between dispatching adapter.notify* events and calculating the updated layout.

在RecyclerView中有两类position的方法。
布局位置：最近一次布局计算后的item位置。这个是从LayoutManager的视角看待的。
适配器位置：适配器的位置。
这两个位置大部分时间都是相同，除了在分发notify*事件和计算并更新布局这段时间。

Methods that return or receive *LayoutPosition* use position as of the latest layout calculation (e.g. getLayoutPosition(),findViewHolderForLayoutPosition(int)). These positions include all changes until the last layout calculation. You can rely on these positions to be consistent with what user is currently seeing on the screen. For example, if you have a list of items on the screen and user asks for the 5thelement, you should use these methods as they'll match what user is seeing.

*LayoutPosition*的方法知识为了返回在最后一次布局计算中的位置。
你可以依赖这个位置来跟用户当前所看的保持一致。譬如，你有一屏幕的内容，用户点击了第五个元素，你可以用这个方法来获取position，它会匹配用户所见

The other set of position related methods are in the form of *AdapterPosition*. (e.g. getAdapterPosition(),findViewHolderForAdapterPosition(int)) You should use these methods when you need to work with up-to-date adapter positions even if they may not have been reflected to layout yet. For example, if you want to access the item in the adapter on a ViewHolder click, you should usegetAdapterPosition(). Beware that these methods may not be able to calculate adapter positions if notifyDataSetChanged() has been called and new layout has not yet been calculated. For this reasons, you should carefully handle NO_POSITION or null results from these methods.

*AdapterPosition*更多是数据集合的位置。你可以在最新的adapter数据并没有刷新布局时获取到位置。
譬如如果你想在ViewHolder点击时访问adapter的item,你可以使用getAdapterPosition()。需要小心的是这些方法可能在通知更新方法调用后，并且新的布局还没有被计算时返回适配器位置。所以你必须要处理NO_POSITION或者null的意外情况。

When writing a RecyclerView.LayoutManager you almost always want to use layout positions whereas when writing anRecyclerView.Adapter, you probably want to use adapter positions.


###LayoutManager

A LayoutManager is responsible for measuring and positioning item views within a RecyclerView as well as determining the policy for when to recycle item views that are no longer visible to the user. By changing the LayoutManager a RecyclerView can be used to implement a standard vertically scrolling list, a uniform grid, staggered grids, horizontally scrolling collections and more. Several stock layout managers are provided for general use.

If the LayoutManager specifies a default constructor or one with the signature (Context, AttributeSet, int, int), RecyclerView will instantiate and set the LayoutManager when being inflated. Most used properties can be then obtained from getProperties(Context, AttributeSet, int, int). In case a LayoutManager specifies both constructors, the non-default constructor will take precedence.

LayoutManager的职责是测量和排位在RecyclerView的item views。并且也有什么时候释放不再对用户可见的itemViews的回收策略的决定权。通过切换LayoutManager 可以用来实现一个标准的纵向滑动列表，一个统一的网格。横向滑动列表。一些常用的layoutmanager已经提供了。
如果LayoutManager制定一个默认的构造器活着其他的非默认构造器，RecyclerView在inflated时将会实例化并设置LayoutManager。大多数使用的属性可以通过getProperties获得。如果LayoutManager同时制定了多个构造器，非默认构造器优先级高。


###Adapter

这个术语就不用多说了，写过listview和gridview的都知道。
