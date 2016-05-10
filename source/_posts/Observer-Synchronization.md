title: Observer Synchronization
date: 2016-04-06 06:42:03
tags:
---
Synchronize multiple screens by having them all be observers to a shared area of domain data.

通过把各个界面作为一个领域数据的公共区域的观察者来保持更新。

In some applications you have multiple screens available that display presentations of a common area of data. If a change is made to the data through one of these screens, you want all the other screens to update correctly. However you don't want each screen to know about the others, because that would increase the complexity of the screens and make it harder to add new ones.

在一些应用中你可能有多个界面来显示一个公共的数据。如果针对其中一个界面的数据进行了修改，你可能需要其他的界面正确地刷新。然而你不希望每一个界面都有强耦合性，因为它会增加界面的复杂度并且为添加新界面制造了难度。

Observer Synchronization uses a single area of domain oriented data and has each screen be an observer of that data. Any change in one screen propagates to that domain oriented data and thence to the other screens. This approach was a large part of the Model View Controller approach.

观察者同步 用了一块单独的领域数据，并且让每一个界面都作为数据的观察者。所有在界面的改动都会通知到领域数据继而通知到其他界面。这个方式是MVC方式的一大部分。

###How it Works

The essence of this approach is that each screen, with its associated screen state, acts as an Observer on a common area of session data. All changes to the session data result in events which the screens listen to and respond by reloading from the session data. Once you have this mechanism set up, then you can ensure synchronization simply by coding each screen to update the session data. Even the screen that makes the change doesn't need to refresh itself explicitly, since the observer mechanism will trigger the refresh in the same way as if another screen made the change.

这个方式的重点在于每一个界面都有其对应的界面状态，它来扮演会话数据公共区域的观察者。所有对于会话数据的改变都会发出界面监听的事件，并且会在事件中重新加载数据。一旦你这种机制建立起来，你可以确保每一个界面的同步非常简单。甚至做出修改的界面都不需要显式自行刷新，因为观察者机制会在其他界面修改的时候以同样的方式触发刷新。

Perhaps the biggest issue in this design is deciding what granularity of events to use and how to set up the propagating and observer relationships. At the very fine-grained level each bit of domain data can have separate events to indicate exactly what changed. Each screen registers for only the events that may invalidate that screen's data. The most coarse grained alternative is to use an Event Aggregator to funnel all events into a single channel. That way each screen does a reload if any piece of domain data changes, whether or not it would have affected the screen.

可能这种设计的最大问题就是事件的粒度是什么，如何建立这种观察者联系。在一些细粒度的系统中，领域数据的每一个点都有对应的事件来标示其改变。每个界面仅仅注册那些刷新界面的数据。粗粒度选项就是使用事件集合器 （类似eventbus那种）将所有的事件都发向一个通道里。在这种方式里每个界面都会在数据改变时重新加载，无论这个数据更改对页面是否有影响。

As usual the trade-off is between complexity and performance. The coarse-grained approach is much simpler to set up and less likely to breed bugs. However a coarse grained approach leads to lots of uneccessary refreshes of the screen, which might impact performace. As usual my advice is to start coarse grained and introduce appropriate fine-grained mechanisms only when needed after measuring an actual performance problem.

通常而言 需要在复杂度和性能方面进行权衡。粗粒度的方式很容易建立并且很少滋生bug。但是它会导致大量的无意义的界面刷新，这些可能会影响性能。通常我的建议是由粗粒度开始，仅仅在需要真正需要优化性能问题时采取合适的细粒度更新机制。

Events tend to be hard to debug since you can't see the chain of invocations by looking at the code. As a result it's important to keep the event propagation mechanism as simple as you can, which is why I favor coarse-grained mechanisms. A good rule of thumb is to think of your objects as layered and to only allow observer relationships between layers. So one domain object should not observe another domain object, only presentation objects should observe domain objects.

事件比较难调试是因为你无法通过代码看到它们的调用链。所以使事件通知机制尽可能地简单很重要，这就是我青睐粗粒度机制的原因。一个好的原则是将你的对象们分层，仅仅只在层间通信时使用观察者。这样的话一个领域对象不应该监控其他的领域对象，只有表现层的对象才能监控领域对象。

Another thing to be very wary of is chains of events, where one event causes another event to fire. Such event chains can quickly get very hard to follow because you can't understand the behavior by looking at the code. As a result I tend to discourage events within a layer and prefer either a single line of events, or going through an Event Aggregator.

还有一个事情需要警惕，就是事件链条，即一个事件会导致另一个事件发生。这些事件链条很容易实现但是很难从代码中看清它的来龙去脉。所以我不鼓励层间事件 并且更喜好单独的时间，或者干脆使用事件总线。

When an observer registers for an event, you get a reference from the subject to the observer. If the observer doesn't remove itself from the subject when you get rid of the screen, you have a zombie reference and a memory leak. If you destroy and create domain data with each session and your session is short, this may not lead to a problem. However a long lived domain object could lead to a serious leak.

当一个观察者关注一个事件时，你得到了一个从subject到observer的引用。如果当你离开这个界面，你并没有移除观察者的话，那么你会有一个僵尸引用可能会导致内存泄漏。如果你创建和摧毁每个会话层的领域对象之间的时间间隔很短，就会导致问题。如果一个长期存活的领域对象会导致很严重的内存泄漏。

###When to Use It

Observer Synchronization is a particularly vital pattern when you have multiple active windows that share common data. In this situation the alternative, having windows signal each other to refresh, gets quite complicated as each window needs to know about other windows and when they are likely to need a refresh. Adding new windows means updating the information. With this approach adding new windows is very straightforward and each window can be setup to maintain its own relationship with the common domain data.

观察者同步是一个很重要的模式，它主要应用于多界面共享公共数据的应用。在这种情形下，一个变种可以持有每一个界面的刷新信号，这样当他们需要刷新的时候他们必须要相互知道，这样会增加不必要的复杂度。添加一个界面意味着更更新信息。通过观察者机制添加一个界面非常直接并且每个界面都能建立和维护自己与数据的联系。

The principal disadvantage of Observer Synchronization is the implicit behavior triggerred by events which is difficult to visualize from the code. As a result bugs in event propagation can be very hard to find and fix.

观察者同步的主要缺点就是行为比较隐性并且行为通过代码不太可见。所以在事件传递过程中产生的bug很难被发现和修复。

Observer Synchronization can also be used with simpler navigational styles, although in these cases Flow Synchronization is a reasonable alternative. The value of Observer Synchronization may not outweigh the complexity introduced by using events for updates.

观察者机制有时候也应用在界面导航比较简单的应用，即使在这些例子中流同步也是一个合理的选择。使用观察者模式带来的事件刷新的复杂度可能会大于使用观察者更新的价值。

###Further Reading

This pattern is obviously very similar to Observer and is a central part of Model View Controller. I see the main difference between this and observer is that this is a particular use of observer - albeit the most common one. I see this part of several patterns that make up Model View Controller.

这个模式跟观察者模式非常接近，它是MVC的核心组成部分。我看到两者的最大的不同就是此模式是观察者模式的特指情况，尽管它是最寻常的一个。

###Acknowledgements

Patrik Nordwall pointed out the problem with observers and memory leaks.

Example: Albums and Performers (C#)

Figure 1

 ![image](http://martinfowler.com/eaaDev/mediatedSynchronization/albumsAndPerformers.jpg)


Figure 1: Screens to display albums and performers.

Consider an application like Figure 1. We have active screens to edit names of performers and the albums they appear from. If I edit the title of the album "Kind of Blue" I want my edits to appear not just in the text box and form title of the album screen, but also in the list entries for Miles Davis and John Coltrane.

考虑图1的应用，你有一个活动窗口可以编辑专辑和作者的名字，如果我更改了"Kind of Blue"专辑的标题，我希望我的修改不仅仅在本页面生效，也在其他页面生效。

Figure 2

 ![image](http://martinfowler.com/eaaDev/mediatedSynchronization/album-class.gif)


Figure 2: Domain objects for album and peformer.

In this case I'm holding the domain data in a couple of simple domain objects for performers and albums Figure 2. When I change the data in one of these classes I need to propagate an event.

在这个例子里，我持有对应的领域对象。  当我更改数据时，我会发出一个事件。

class Album : DomainObject
  public string Title {
    get { return _title; }
    set {
      _title = value;
      SignalChanged();
    }
  }
  string _title;
class DomainObject...
  public void SignalChanged() {
    if (Changed != null) Changed (this, null);      
  }
  public event DomainChangeHandler Changed;
public delegate void DomainChangeHandler (DomainObject source, EventArgs e);

Here I just have a simple changed event defined in a Layer Supertype. This event doesn't give any information about the change, just that some change has occurred - suitable for a coarse-grained synchronization from the client.

在这里我只是简单定义了一个变化事件，这个事件并不能带回任何信息，只是通知变化发生了，适用于客户端的粗粒度更新。

In the form, I create a new album form by passing in an album. The constructor does its usual gui stuff, sets the album reference, wires up the event listeners and finally loads data from the album.

在界面中，我会创建一个新的专辑对象来传递进去。构造器会做它应该做的界面加载工作，并设置专辑对象的引用。（此处相当于view引用了model）
串联起事件监听器并在最后从model中加载数据。

class FrmAlbum...
  public FrmAlbum(Album album)    {
    InitializeComponent();
    this._album = album;
    observeDomain();
    load();
  }
  private Album _album;
  private void observeDomain() {
    _album.Changed += new DomainChangeHandler(Subject_Changed);
    foreach (Performer p in _album.Performers) 
      p.Changed +=new DomainChangeHandler(Subject_Changed);
  }
  private void Subject_Changed(DomainObject source, EventArgs e)  {
    load();
  }
  
The event listening is wired up so that any change in a dependent domain object causes the form to reload its data.

事件监听会向下传递，所以对领域对象的改变会导致数据重加载。

class FrmAlbum...
  private void load() {
    txtTitle.Text = _album.Title;
    this.Text = _album.Title;
    lstPerformers.DataSource = performerNames();
  }
  private string[] performerNames() {
    ArrayList result = new ArrayList();
    foreach (Performer p in _album.Performers) 
      result.Add(p.Name);
    return (string[]) result.ToArray(typeof (string));
  }
  
If I change the title in the album, I make that change directly on the underlying domain object.

当更改专辑标题时，我会直接改变后台的领域对象。

class FrmAlbum...
  private void txtTitle_TextChanged(object sender, EventArgs e) {
    this._album.Title = txtTitle.Text;
  }
  
  
You could acheive much of this using data binding as long as each form can participate in the binding with the shared domain objects. Another variation would be to use an Event Aggregator - this would allow each form to register only with the aggregator and not to have to register with each domain object. In the absence of performance issues, I would do it that way - I didn't here as I prefer to keep examples as independent of each other as possible.

只要每个界面可以同共享的领域对象绑定，你可以使用数据绑定技术来省不少事。另一种方式就是使用事件集合器，它会允许每个界面注册到事件集合器本身即可，而不用针对每个领域对象注册事件。如果不考虑性能因素，我一般会这么做，我不在这里演示只是让每个个体保持独立而已。

If you're using Supervising Controller or Passive View the controller would act as the observer of the domain events. If you're using Presentation Model then the Presentation Model would act as the observer.

如果你要使用Supervising Controller或者Passive View模式，控制器来扮演领域事件监听器的角色。如果你使用Presentation Model，那么Presentation Model会充当观察者。
