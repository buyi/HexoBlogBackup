title: Presentation Model
date: 2016-03-15 14:46:00
tags:
---
Presentation Model

Represent the state and behavior of the presentation independently of the GUI controls used in the interface

独立地表示在界面中使用的GUI控件的呈现的状态和行为。

GUIs consist of widgets that contain the state of the GUI screen. Leaving the state of the GUI in widgets makes it harder to get at this state, since that involves manipulating widget APIs, and also encourages putting presentation behavior in the view class.

GUIs 一般包括包含整体界面状态的控件。将GUI的状态留在控件中，会使得获取GUI的状态尤为困难，因为还需要操作控件的API，并且这样也将鼓励把呈现行为放在view类中。

Presentation Model pulls the state and behavior of the view out into a model class that is part of the presentation. The Presentation Model coordinates with the domain layer and provides an interface to the view that minimizes decision making in the view. The view either stores all its state in the Presentation Model or synchronizes its state with Presentation Model frequently

Presentation Model把view的状态和行为从view中拉出放到model中，并且model类是表现层的一部分。Presentation Model与领域层相互协作，并且提供给view接口，以最小化view能做的决定。View不仅可以在Presentation Model中存储所有的状态也可以经常同步Presentation Model中的状态。

Presentation Model may interact with several domain objects, but Presentation Model is not a GUI friendly facade to a specific domain object. Instead it is easier to consider Presentation Model as an abstract of the view that is not dependent on a specific GUI framework. While several views can utilize the same Presentation Model, each view should require only one Presentation Model. In the case of composition a Presentation Model may contain one or many child Presentation Model instances, but each child control will also have only one Presentation Model.

Presentation Model可能会跟一些领域对象交互，但是Presentation Model对于特定领域对象并不是一个用户界面友好的入口。相反它通常会很容易认为Presentation Model是一个独立特定GUI框架的view的抽象。一些view可以使用同一个Presentation Model，每一个view应该只要求一个Presentation Model。在组合Presentation Model可能包含一个或者多个子Presentation Model时，每一个子控制器也应该仅仅有一个Presentation Model。

Presentation Model is known to users of Visual Works Smalltalk as Application Model

Presentation Model就是Application Model （在Visual Works中）

How it Works

The essence of a Presentation Model is of a fully self-contained class that represents all the data and behavior of the UI window, but without any of the controls used to render that UI on the screen. A view then simply projects the state of the presentation model onto the glass.

Presentation Model的基础应该是一些完全自包含的类，用来表示UI视窗的所有数据和行为，但是不包括界面上用来渲染UI的控件。View可以简单的将Presentation Model的状态反射出来。

To do this the Presentation Model will have data fields for all the dynamic information of the view. This won't just include the contents of controls, but also things like whether or not they are enabled. In general the Presentation Model does not need to hold all of this control state (which would be lot) but any state that may change during the interaction of the user. So if a field is always enabled, there won't be extra data for its state in the Presentation Model.

为了达到这个目的，Presentation Model将持有所有view的动态信息的数据变量。这将不仅仅包括控件的内容，也包括他们能否可以使用的标记为。通常而言，Presentation Model不需要持有所有的（也许是大量的）的控件状态，但会有一些状态会在用户交互过程中改变。所以如果一个数据变量一直处于可用状态，在Presentation Model不应该有它的状态的额外数据。

Since the Presentation Model contains data that the view needs to display the controls you need to synchronize the Presentation Model with the view. This synchronization usually needs to be tighter than synchronization with the domain - screen synchronization is not sufficient, you'll need field or key synchronization.

因为Presentation Model包含视图需要显示的数据。你需要同步view和Presentation Model。这种同步机制通常比与domain更紧耦合。screen同步基本都是不足的，你需要变量或者关键字同步。

To illustrate things a bit better, I'll use the aspect of the running example where the composer field is only enabled if the classical check box is checked.

为了证实上述事实，我会使用一个跑步的例子，其中组合变量仅仅在经典选择框选中后才能生效

Figure 1


 ![image]( http://www.martinfowler.com/eaaDev/presentationModel/cutClass.gif)


Figure 1: Classes showing structure relevant to clicking the classical check box

类图

Figure 2

 ![image]( http://www.martinfowler.com/eaaDev/presentationModel/cutSeq.gif)

Figure 2: How objects react to clicking the classical check box.

如何响应checkbox点击事件

When someone clicks the classical check box the check box changes its state and then calls the appropriate event handler in the view. This event handler saves the state of the view to Presentation Model and then updates itself from the Presentation Model (I'm assuming a coarse-grained synchronization here.) The Presentation Model contains the logic that says that the composer field is only enabled if the check box is checked, so the when the view updates itself from the Presentation Model, the composer field control changes its enablement state. I've indicated on the diagram that the Presentation Model would typically have a property specifically to mark whether the composer field should be enabled. This will, of course, just return the value of the isClassical property in this case - but the separate property is important because that property encapsulates how the Presentation Model determines whether the composer field is enabled - clearly indicating that that decision is the responsibility of the Presentation Model.

当有人点击经典的选择框时，选择框会改变它的状态，调用合适的事件处理器。事件处理器会在Presentation Model 存储对应view的状态，并且会自我更新。Presentation Model 包含业务逻辑：组合器输入框只有在选择框选中后才能生效。所以当view从Presentation Model 更新自己的状态时，组合器输入框更新了自己的开关。我在图中提到了Presentation Model 应该包括一个变量用来标识组合器输入框是否可用。在这个例子里 仅仅简单的返回isClassical属性值。但是分离这个属性非常重要，因为这个属性封装了Presentation Model 决定组合器输入框是否可用的逻辑。非常清楚地指出了 这是Presentation Model 的责任。

This small example is illustrates the essence of the idea of the Presentation Model - all the decisions needed for presentation display are made by the Presentation Model leaving the view to be utterly simple.

这个简小的例子说明了Presentation Model 想法的本质。所有显示层的程序逻辑所做的各种各种的所有的决定都由Presentation Model 搞定，使得view异常地简单。

Probably the most annoying part of Presentation Model is the synchronization between Presentation Model and view. It's simple code to write, but I always like to minimize this kind of boring repetitive code. Ideally some kind of framework could handle this, which I'm hoping will happen some day with technologies like .NET's data binding.

可能大多数Presentation Model 的烦人的部分都是Presentation Model 和view之间的同步。这里的代码非常简单，但是我经常想最小化这些烦人的重复代码。理想状态下，一些框架能够处理这个问题，我希望通过像data binding这样的技术能够解决。

A particular decision you have to make with synchronization in Presentation Model is which class should contain the synchronization code. Often, this decision is largely based on the desired level of test coverage and the chosen implementation of Presentation Model. If you put the synchronization in the view, it won't get picked up by tests on the Presentation Model. If you put it in the Presentation Model you add a dependency to the view in the Presentation Model which means more coupling and stubbing. You could add a mapper between them, but adds yet more classes to coordinate. When making the decision of which implementation to use it is important to remember that although faults do occur in synchronization code, they are usually easy to spot and fix (unless you use fine-grained synchronization).

在与对于Presentation Model同步这个过程中，应该哪个类里包含同步代码，你需要做个决定。通常而言，这个决定基本上要基于我们想要的测试覆盖度的等级，来选择对应的Presentation Model的实现。如果你想把同步逻辑放在view中，则它将不会再Presentation Model的测试中获取到。如果把同步逻辑放在Presentation Model中，并且在里边添加一个对view的依赖关系，意味着更多的耦合。你可以在他们中间添加一个映射，也可以添加更多的类来协调逻辑。当你决定使用哪个实现的的时候，最重要的是当你的同步代码出错，一定要很容易的被找到和修复。除非你使用更细粒度的同步。

An important implementation detail of Presentation Model is whether the View should reference the Presentation Model or the Presentation Model should reference the View. Both implementations provide pros and cons.

在Presentation Model实现的细节中，最重要的一条就是view是否应该引用Presentation Model或者Presentation Model是否应该引用View。两种实现都有各自的优点和缺点。

A Presentation Model that references a view generally maintains the synchronization code in the Presentation Model. The resulting view is very dumb. The view contains setters for any state that is dynamic and raises events in response to user actions. The views implement interfaces allowing for easy stubbing when testing the Presentation Model. The Presentation Model will observe the view and respond to events by changing any appropriate state and reloading the entire view. As a result the synchronization code can be easily tested without needing the actual UI class.

Presentation Model引用View，通常而言会把同步逻辑包含在Presentation Model中。这样的话，view通常会比较缺乏内容。view会包含所有动态状态的设置器并且能够在响应用户事件时发出事件。当测试Presentation Model时，view的实现接口可以允许很容易的介入.Presentation Model通常可以观察view并且响应通过更改对应状态发出的事件来重新加载对应的view。这样做的结果就是同步代码在没有真正UI类的情况下很容易被测试，


A Presentation Model that is referenced by a view generally maintains the synchronization code in the view. Because the synchronization code is generally easy to write and easy to spot errors it is recommended that the testing occur on the Presentation Model and not the View. If you are compelled to write tests for the view this should be a clue that the view contains code that should belong in the Presentation Model. If you prefer to test the synchronization, a Presentation Model that references a view implementation is recommended.

Presentation Model被一个view引用通常会把同步逻辑写在View中。因为同步代码通常很容易写并且很容易指出错误。通常推荐在Presentation Model中测试而不是View。如果你强迫为view写测试，那么肯定view中会包含本来属于Presentation Model的代码。如果你更想测试同步逻辑，一个Presentation Model引用view的实现更加被推荐。

When to Use It

Presentation Model is a pattern that pulls presentation behavior from a view. As such it's an alternative to to Supervising Controller and Passive View. It's useful for allowing you to test without the UI, support for some form of multiple view and a separation of concerns which may make it easier to develop the user interface.

Presentation Model是一种将展现逻辑拉出view的模式。同时它也是Supervising Controller和Passive View的一个变种。它会允许你在没有界面的情况下进行测试，支持多种视图，并且分离了一些依赖关系，使得开发界面更简单。

Compared to Passive View and Supervising Controller, Presentation Model allows you to write logic that is completely independent of the views used for display. You also do not need to rely on the view to store state. The downside is that you need a synchronization mechanism between the presentation model and the view. This synchronization can be very simple, but it is required. Separated Presentation requires much less synchronization and Passive View doesn't need any at all.

对比Passive View和Supervising Controller。Presentation Model 允许你写完全独立于view显示的逻辑。你再也不需要依赖视图来存储状态。缺陷就是你需要在Presentation Model 和view之间需要某种同步机制。这个同步机制可能会非常简单，但是是必须的。分离呈现不需要太多的同步，而Passive View是根本就不需要。

Example: Running Example (View References PM) (C#)

Here's a version of the running example, developed in C# with Presentation Model.

Figure 3

 ![image](http://www.martinfowler.com/eaaDev/presentationModel/netAlbum.jpg)


Figure 3: The album window.

I'll start discussing the basic layout from the domain model outwards. Since the domain isn't the focus of this example, it's very uninteresting. It's essentially just a data set with a single table holding the data for an album. Here's the code for setting up a few test albums. I'm using a strongly typed data set.

我将从domain model开始向外讨论。因为domain不是本例子的重点，所以不太有趣哦。建立一个数据集合来持有相册数据时很有必要的。这里建立了一些测试的albums，我用了一个强类型的数据集。

public static DsAlbum AlbumDataSet() {
  DsAlbum result = new DsAlbum();
  result.Albums.AddAlbumsRow(1, "HQ", "Roy Harper", false, null);
  result.Albums.AddAlbumsRow(2, "The Rough Dancer and Cyclical Night", "Astor Piazzola", false, null);
  result.Albums.AddAlbumsRow(3, "The Black Light", "Calexico", false, null);
  result.Albums.AddAlbumsRow(4, "Symphony No.5", "CBSO", true, "Sibelius" );
  result.AcceptChanges();
  return result;
}

The Presentation Model wraps this data set and provides properties to get at the data. There's a single instance of the Presentation Model for the whole table, corresponding to the single instance of the window. The Presentation Model has fields for the data set and also keeps track of which album is currently selected.

Presentation Model 包含了这个数据集并且提供了属性以供访问。在这里，整张表只有一个Presentation Model的对象，对应一个视窗对象。Presentation Model有一个数据集合的对象，并且也跟踪哪个album被选中。

class PmodAlbum...
  public PmodAlbum(DsAlbum albums) {
    this._data = albums; 
    _selectedAlbumNumber = 0;
  }
  private DsAlbum _data;
  private int _selectedAlbumNumber;
  
  
PmodAlbum provides properties to get at the data in the data set. Essentially I provide a property for each bit of information that the form needs to display. For those values which are just pulled directly out of the data set, this property is pretty simple.

PmodAlbum （Presentation Model Album） 提供属性以供访问数据集合的数据。我们也提供一些需要显示信息的细分版本。对于这些值，它们可以直接从数据集合中拉取，对应的属性非常简单。

class PmodAlbum...
  public String Title {
    get {return SelectedAlbum.Title;}
    set {SelectedAlbum.Title = value;}
  }
  public String Artist {
    get {return SelectedAlbum.Artist;}
    set {SelectedAlbum.Artist = value;}     
  }
  public bool IsClassical {
    get {return SelectedAlbum.IsClassical;}
    set {SelectedAlbum.IsClassical = value;}            
  }
  public String Composer {
    get {
      return (SelectedAlbum.IsComposerNull()) ? "" : SelectedAlbum.Composer;
    }
    set {
      if (IsClassical) SelectedAlbum.Composer = value;
    }                 
  }
  public DsAlbum.AlbumsRow SelectedAlbum {
    get {return Data.Albums[SelectedAlbumNumber];}
  }
  
  
The title of the window is based on the album title. I provide this through another property.

对于视窗的标题，实际上是基于相册标题的。所以我为此提供了另一个单独的属性。

class PmodAlbum...
  public String FormTitle 
  {
    get {return "Album: " + Title;}
  }
  
I have a property to see if the composer field should be enabled.

并且我会有一个属性来观察，作曲家的输入框是否可用。

class PmodAlbum...
  public bool IsComposerFieldEnabled {
    get {return IsClassical;}
  }
  
This is just a call to the public IsClassical property. You may wonder why the form doesn't just call this directly - but this is the essence of the encapsulation that the Presentation Model provides. PmodAlbum decides what the logic is for enabling that field, the fact that it's simply based on a property is known to the Presentation Model but not to the view.

在这里，我仅仅是调用公开的IsClassical的属性。你可能想知道为什么不直接调用这个属性，但是这个是Presentation Model 提供封装性的根本。PmodAlbum来决定输入框是否可用的逻辑，这个事实基于Presentation Model 的属性，而不取决于view。

The apply and cancel buttons should only be enabled if the data has changed. I can provide this by checking the state of that row of the dataset, since data sets record this information.

应用&取消按钮应该仅仅在数据发生变化时可用。我可以通过检查数据集合行的状态，来告知数据是否变化。

class PmodAlbum...
  public bool IsApplyEnabled {
    get {return HasRowChanged;}
  }
  public bool IsCancelEnabled {
    get {return HasRowChanged;}
  }
  public bool HasRowChanged {
    get {return SelectedAlbum.RowState == DataRowState.Modified;}
  }
  
The list box in the view shows a list of the album titles. PmodAlbum provides this list.

listbox 需要显示相册标题的列表，PmodAlbum提供这个列表。

class PmodAlbum...
  public String[] AlbumList {
    get {
      String[] result = new String[Data.Albums.Rows.Count];
      for (int i = 0; i < result.Length; i++)
        result[i] = Data.Albums[i].Title;
      return result;
    }
  }
  
  
So that covers the interface that PmodAlbum presents to the view. Next I'll look at how I do the synchronization between the view and the Presentation Model. I've put the synchronization methods in the view and am using coarse-grained synchronization. First I have a method to push the state of the view into the Presentation Model.

这样通过这个接口，PmodAlbum就可以呈现给view。接下来我们来看我是如何做view 和 Presentation Model之间的同步。我会将同步方法防盗view中并且使用粗粒度的同步机制。首先我需要有一个方法可以在Presentation Model保存view的状态

class FrmAlbum...
  private void SaveToPmod() {
    model.Artist = txtArtist.Text;
    model.Title = txtTitle.Text;
    model.IsClassical = chkClassical.Checked;
    model.Composer = txtComposer.Text;
  }
  
This method is very simple, just assigning the mutable parts of the view to the Presentation Model. The load method is a touch more complicated.

这个方法很简单，仅仅是把view交互部分的值赋给Presentation Model。加载方法可能有一点复杂。

class FrmAlbum...
  private void LoadFromPmod() {
    if (NotLoadingView) {
      _isLoadingView = true;
      lstAlbums.DataSource = model.AlbumList;
      lstAlbums.SelectedIndex = model.SelectedAlbumNumber;
      txtArtist.Text = model.Artist;
      txtTitle.Text = model.Title;
      this.Text = model.FormTitle;
      chkClassical.Checked = model.IsClassical;
      txtComposer.Enabled = model.IsComposerFieldEnabled;
      txtComposer.Text = model.Composer;
      btnApply.Enabled = model.IsApplyEnabled;
      btnCancel.Enabled = model.IsCancelEnabled;
      _isLoadingView = false;
    }
  }
  
  private bool _isLoadingView = false;
  private bool NotLoadingView {
    get {return !_isLoadingView;}
  }
  
private void SyncWithPmod() {
  if (NotLoadingView) {
    SaveToPmod();
    LoadFromPmod();
  }
}

The complication here is avoiding a infinite recursion since synchronizing causes fields on the form to update which triggers synchronization.... I guard against that with a flag.

这里的有个问题就是当刷新触发同步逻辑时可能会导致无限循环，这里用了一个标记位来预防此问题。

With these synchronization methods in place, the next step is just to call the right bit of synchronization in event handlers for the controls. Most of the time this easy, just call SyncWithPmod when data changes.

有了这些同步方法，下一步就是在控制流离的事件处理器中正确地调用。大部分时间很简单，只在数据变化的时候调用SyncWithPmod方法。

class FrmAlbum...
  private void txtTitle_TextChanged(object sender, System.EventArgs e){
    SyncWithPmod();
  }
  
Some cases are more involved. When the user clicks on a new item in the list we need to navigate to a new album and show its data.

还有一些场景，譬如用户在左侧的列表里点击了一个新的选项，我们需要切换到那个相册并呈现它的数据。

class FrmAlbum...
  private void lstAlbums_SelectedIndexChanged(object sender, System.EventArgs e){
    if (NotLoadingView) {
      model.SelectedAlbumNumber = lstAlbums.SelectedIndex;  
      LoadFromPmod();
    }
  }
  
class PmodAlbum...
  public int SelectedAlbumNumber {
    get {return _selectedAlbumNumber;}
    set {
      if (_selectedAlbumNumber != value) {
        Cancel();
        _selectedAlbumNumber = value;
      }
    }
  }
  
Notice that this method abandons any changes if you click on the list. I've done this awful bit of usability to keep the example simple, the form should really at least pop up a confirmation box to avoid losing the changes.

注意在这里 如果你点击了左侧的列表，这个方法会放弃你所做的所有修改。我在这里为了保持例子精简做了一些牺牲。真实程序中应该弹出一个对话框来让用户确认是否要丢失这些修改。

The apply and cancel buttons delegate what to do to the Presentation Model.

apply和cancel按钮也都是通过代理，来Presentation Model 真正做事。

class FrmAlbum...
  private void btnApply_Click(object sender, System.EventArgs e)    {
    model.Apply();
    LoadFromPmod();
  }
  
  private void btnCancel_Click(object sender, System.EventArgs e){
    model.Cancel();
    LoadFromPmod();
  }
  
class PmodAlbum...
  public void Apply ()    {
    SelectedAlbum.AcceptChanges();
  }
  
  public void Cancel() {
    SelectedAlbum.RejectChanges();
  }
  
So although I can move most of the behavior to the Presentation Model, the view still retains some intelligence. For the testing aspect of Presentation Model to work better, it would be nice to move more. Certainly you can move more into the Presentation Model by moving the synchronization logic there, at the expense of having the Presentation Model know more about the view.

尽管我迁移了大部分的业务逻辑到Presentation Model，这个view还是有相当的代码。为了Presentation Model的测试方面能表现得更好，应该再多挪一点。所以你可以把同步逻辑挪到Presentation Model中，得到的代价就是让Presentation Model 与view的耦合深一点。


Example: Data Binding Table Example (C#)

As I first looked at Presentation Model in the .NET framework, it seemed that data binding provided excellent technology to make Presentation Model work simply. So far limitations in the current version of data binding holds it back from places that I'm sure it will eventually go. One area where data binding can work very well is with read-only data, so here is an example that shows this as well as how tables can fit in with a Presentation Model design.

当我第一次在.net框架中看到Presentation Model时，我就觉得数据绑定技术能够让Presentation Model工作得更简单。尽管当前的数据绑定仍有缺陷，我确信它能不断进步。在只读数据领域里数据绑定技术能工作得很好。所以这里有一个例子来展现Presentation Model 设计是如何填充表格数据的。

Figure 4


 ![image](http://www.martinfowler.com/eaaDev/presentationModel/netAlbumList.jpg)

Figure 4: A list of albums with the rock ones highlighted.

This is just a list of albums. The extra behavior is that each rock album should have it's row colored in cornsilk.

这仅仅是相册列表。多余的行为就是每个rock的相册都会被刷成玉米色。

I'm using a slightly different data set to the other example. Here is the code for some test data.

我会使用一个略微不同的数据集，以下为测试数据的代码。

public static AlbumList AlbumGridDataSet() 
{
  AlbumList result = new AlbumList();
  result.Albums.AddAlbumsRow(1, "HQ", "Roy Harper", "Rock");
  result.Albums.AddAlbumsRow(2, "Lemonade and Buns", "Kila", "Celtic");
  result.Albums.AddAlbumsRow(3, "Stormcock", "Roy Harper", "Rock");
  result.Albums.AddAlbumsRow(4, "Zero Hour", "Astor Piazzola", "Tango");
  result.Albums.AddAlbumsRow(5, "The Rough Dancer and Cyclical Night", "Astor Piazzola", "Tango");
  result.Albums.AddAlbumsRow(6, "The Black Light", "Calexico", "Rock");
  result.Albums.AddAlbumsRow(7, "Spoke", "Calexico", "Rock");
  result.Albums.AddAlbumsRow(8, "Electrica", "Daniela Mercury", "Brazil");
  result.Albums.AddAlbumsRow(9, "Feijao com Arroz", "Daniela Mercury", "Brazil");     
  result.Albums.AddAlbumsRow(10, "Sol da Libertade", "Daniela Mercury", "Brazil");  
  Console.WriteLine(result);
  return result;
}

The presentation model in this case reveals its internal data set as a property. This allows the form to data bind directly to the cells in the data set.

在这个例子里presentation model 直接用属性来暴露内部的数据集合。它允许表格的数据直接映射到数据集里的单元。

private AlbumList _dsAlbums;
internal AlbumList DsAlbums {
  get {return _dsAlbums;}
}

To support the highlighting, the presentation model provides an additional method that indexes into the table.

为了支持高亮，presentation model 提供一个方法来索引

internal Color RowColor(int row) {
  return (Albums[row].genre.Equals("Rock")) ? Color.Cornsilk : Color.White;
}
private AlbumList.AlbumsDataTable Albums {
  get {return DsAlbums.Albums;}
}

This method is similar to the ones in a simple example, the difference being that methods on table data need cell coordinates to pick out parts of the table. In this case all we need is a row number, but in general we may need row and column numbers.

这个方法跟前边简单的例子很类似，不同的是我们可能需要取到表格的部分数据来与单元格同步。在这个例子里，我们需要的就是行号，但我们也可能需要列号。

From here on I can use the standard data binding facilities that come with visual studio. I can bind table cells easily to data in the data set, and also to data on the Presentation Model.

在这里我可以使用vs中典型的数据绑定工具。我能够很简单地绑定表格的单元格到数据集里的数据，也可以绑定到presentation model 中的数据。

Getting the color to work is a little bit more involved. This is straying a little bit away from the main thrust of the example, but the whole thing gets its complication because there's no way to have row by row highlighting on the standard WinForms table control. In general the answer to this need is to buy a third party control, but I'm too cheap to do this. So for the curious here's what I did (the idea was mostly ripped off from http://www.syncfusion.com/FAQ/WinForms/). I'm going to assume you're familiar with the guts of WinForms from now on.

接下来就是获取颜色。这个有可能偏离正轨，但是这里有点麻烦就是因为在WinForm表格控件中确实没有一个方法能够提供高亮控制。通常而言就是购买一个第三方空间，但在这里我自己动手丰衣足食。如果好奇我这里怎么做的，你可以看下边这篇文章（http://www.syncfusion.com/FAQ/WinForms/），在这里我假设你们对与WinForm很熟悉。

Essentially I made a subclass of DataGridTextBoxColumn which adds the color highlighting behavior. You link up the new behavior by passing in a delegate that handles the behavior.

接下来比较重要的事情就是写DataGridTextBoxColumn的子类，把方法高亮的逻辑加进去。你可以通过传递一个处理行为的代理对象来链接新的行为。

class ColorableDataGridTextBoxColumn...
  public ColorableDataGridTextBoxColumn (ColorGetter getcolorRowCol, DataGridTextBoxColumn original)
  {
    _delGetColor = getcolorRowCol;
    copyFrom(original);
  }
  public delegate Color ColorGetter(int row);
  private ColorGetter _delGetColor;
  
The constructor takes the original DataGridTextBoxColumn as well as the delegate. What I'd really like to do here is to use the decorator pattern to wrap the original but the original, like so many classes in WinForms, is all sealed up. So instead I copy over all the properties of the original into my subclass. This won't work is there are vital properties that can't be copied because you can't read or write to them. It seems to work here for now.

这个构造方法利用原始的Column作为装饰者。我这里真正做的就是像其他WinForms的类一样，利用装饰模式将原始Column包装起来。所以我copy所有的原始对象的值到我的子类。有些重要的属性没法复制是因为你不能读写他们，在这里还好。

class ColorableDataGridTextBoxColumn...
  void copyFrom (DataGridTextBoxColumn original) {
    PropertyInfo[] props = original.GetType().GetProperties();
    foreach (PropertyInfo p in props) {
      if (p.CanWrite && p.CanRead)
        p.SetValue(this, p.GetValue(original, null), null) ;
    }
  }
  
Fortunately the paint method is virtual (otherwise I would need a whole new data grid.) I can use it to insert the appropriate background color using the delegate.

幸运的是paint方法是虚方法。我可以通过代理插入相应的背景图片。

class ColorableDataGridTextBoxColumn...
  protected override void Paint(System.Drawing.Graphics g, System.Drawing.Rectangle bounds, 
    System.Windows.Forms.CurrencyManager source, int rowNum, 
    System.Drawing.Brush backBrush, System.Drawing.Brush foreBrush, 
    bool alignToRight)
  {
    base.Paint(g, bounds, source, rowNum, new SolidBrush(_delGetColor(rowNum)), foreBrush, alignToRight);
  }
To put this new table in place, I replace the columns of the data table in the page load after the controls have been built on the form.

将新的表格替换。我在表格的控件加载完毕后替换。

class FrmAlbums...
  private void FrmAlbums_Load(object sender, System.EventArgs e){
    bindData();
    replaceColumnStyles();
  }
  private void replaceColumnStyles() {
    ColorableDataGridTextBoxColumn.ReplaceColumnStyles(dgsAlbums, 
      new ColorableDataGridTextBoxColumn.ColorGetter(model.RowColor));
  }
  
class ColorableDataGridTextBoxColumn...
  public static void ReplaceColumnStyles(DataGridTableStyle grid, ColorGetter del) {
    for (int i = 0; i < grid.GridColumnStyles.Count; i++) {
      DataGridTextBoxColumn old = (DataGridTextBoxColumn) grid.GridColumnStyles[0];
      grid.GridColumnStyles.RemoveAt(0);
      grid.GridColumnStyles.Add(new ColorableDataGridTextBoxColumn(del, old));
    }
  }
It works, but I'll admit it's a lot more messy than I would like. If I were doing this for real, I'd want to look into a third party control. However I've seen this done in a production system and it worked just fine.

它能够工作，但是我承认 这块有一点凌乱。如果我去做一个真实的案例，我想找一个第三方空间。但现在这个原型工作得还好。
