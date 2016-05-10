title: Supervising Controller
date: 2016-03-14 15:45:43
tags:
---
Supervising Controller

Factor the UI into a view and controller where the view handles simple mapping to the underlying model and the the controller handles input response and complex view logic.

将UI分成两个部分View和Controller。
View会简单处理与model的映射关系。
Controller会处理用户输入并且有复杂的view逻辑

Many UI frameworks provide the ability to easily map between the view and model, often using some kind of Data Binding. These approaches are very effective in allowing you to declaratively set up a relationship between elements in the view and model. Usually, however, there are more complex relationships which require you to have more complex view logic. This logic can be hard to manage, and in particular hard to test, while embedded in the view.

一些比较著名的Data Binding框架提供view和model的简单映射关系。这种方式可以让你鲜明地建立view和model中元素的联系。通常而言，非常复杂的关系会让你有更复杂的视图逻辑。这种逻辑嵌在view里的很难被管理，也很难被测试。

Supervising Controller uses a controller both to handle input response but also to manipulate the view to handle more complex view logic. It leaves simple view behavior to the declarative system, intervening only when effects are needed that are beyond what can be achieved declaratively.

Supervising Controller作为控制器，可以处理用户输入也可以操作view来完成复杂的view逻辑。它为一个纯粹的系统留下了相当简单的view逻辑。只有在需要效果超过它声明范围时，才会进行干预。

How it Works

Supervising Controller decomposes presentation functionality into two parts: a controller (often called presenter) and view. The domain data that needs to be displayed is separate, and following rough MVC terminology I'll refer to it as a model, although it need not be a Domain Model. The basic division of responsibilities echoes the Model-View-Presenter architecture in its Dolphin form, as described by Bower and McGlashan.

Supervising Controller解耦了显示功能，将它分成了两个部分。一个是Controller 通常也叫做Presenter，另一个是View。需要显示的领域数据一定是可分离的。而且接下来根据原始的MVC名词，我将它指为Model即使它不必是一个领域模型。这种基本的职责分配按照Dolphin的格式一般叫MVP

A Supervising Controller has two primary responsibilities: input response and partial view/model synchronization.

Supervising Controller主要有两个职责。一个是输入响应，另外一个是view/model部分同步功能

For input response the controller operates in the presenter style. The user gestures are handled initially by the screen widgets, however all they do in response is to hand these events off to the presenter, which handles all further logic.

对于输入响应的展现逻辑，控制器是这样操作的（presenter style）。用户动作最初由屏幕widget生成，但是它所有的响应都推给presenter，presenter处理所有的逻辑。

For view/model synchronization the controller defers as much of this as reasonable to the view. The view typically uses some form of Data Binding to populate much of the information for its fields. Where Data Binding isn't up to more complex interactions then the controller steps in.

对于view/model 同步，控制器会将相当多比较合理的职能推给view。view通常使用类似Data Binding的技术来为他们的field组装数据。数据绑定不应该处理复杂的交互，因为控制器会介入。

Figure 1

![image](http://www.martinfowler.com/eaaDev/supervisingPresenter/assess-cd.gif)

Figure 1: Class diagram for the assessment example.
类图

Figure 2

![image](http://www.martinfowler.com/eaaDev/supervisingPresenter/assess-seq.gif)

Figure 2: Sequence diagram showing the response for putting in a low actual value.
时序图

In the assessment window, the initial text change is handled by the text field widget in the view. This widget is observed by the controller, so when the text changes the widgets emits an event which results in the controller's actualFieldChanged method being called. This method then handles the full response to the event. It first updates the reading model object's actual value. The window observes the reading object so the change to the reading's value triggers a refresh. It's pretty easy to map the actual and variance text fields' text to the appropriate properties of the reading, so that updates those values. For the purposes of our example changing the color is a bit more involved. The reading object can, and should, determine which category the reading should fit into. A sophisticated widget might be able to bind its text color to a category like this, but we'll assume we don't have such a clever young thing. In this case controller takes over setting the variance field's text color directly.

在这个例子里，最初的文字变化是在view中的文本输入框中被处理的。这个控件被控制器观察着，所以当文本改变时，控件会发出一个文本改变的事件，它会导致控制器的actualFieldChanged 方法被调用。这个方法来全权处理事件的所有响应。 它第一件事就是更新背后的值模型的值，因为View也监控着值对象，所以值模型的值改变会出发一个更新。直接映射actual和variance的控件的文本到对应的值模型的域是轻而易举的事情。，所以值会被更新。对于我们的例子的初衷，更改颜色也是一个需求。值模型可以也应该能决定它将涂上哪种颜色。一个精致的空间通常可以绑定他们的文字颜色到一个值模型中的类别变量。但是我们假设我们并没有一个这么一个聪明的机制。在本例子中，控制器接管了设置variance字体颜色的职能。

As the example shows, the essence of a good Supervising Controller is to do as little as possible. Let the view handle as much as possible and only step in when there's more complex logic involved.

就像上述例子展现的那样，一个好的监督式控制器的本质应该是尽可能做得少，让view来尽可能多的处理逻辑，仅仅在需要复杂逻辑的时候介入。

One of the prime reasons to use Supervising Controller is for testability. Assuming the view is hard to test, by moving any complex logic into the controller, we put the logic in a place that's easier to test. In order to run tests on the controller, however, we do need some form of view, so a Test Double is often in order. With the double in place, we don't have any need for UI framework objects in order to test the more awkward parts of the UI behavior.

使用监督式控制器的一个基本原因是可测试性。想象一下view一般都是难以测试，通过把复杂的逻辑挪到控制器，我们可以把逻辑放在一个好测试的地方。为了运行针对控制器的测试，我们需要各种格式的view，所以Test Double通常是按照顺序的。通过一个Test Double，我们不需要任何的UI控件来测试那些难看的UI行为部分。

This testability issue affects another decision - whether the controller should access the view and its widgets directly, or through an intermediary. With an intermediary we build a Gateway for the the controller. The gateway defines an interface for the controller to manipulate. One implementation then adapts the window's interface while the other provides a stub for testing (you could also use a mock). This is the same approach that you need for Passive View.

可测试性 还影响着另一个决定。控制器是否应该直接操纵view和它的控件，直接或者是间接。通过一个中介，我们可以为控制器建立一个网关。网关定义了一个控制器可以操作的接口。一个实现通常可以适应界面的接口，其他的可以为测试提供一个桩（也可以使用模拟器。）这个也是Passive View的需要性的一部分。

Figure 3


![image](http://www.martinfowler.com/eaaDev/supervisingPresenter/asssesIntermed-cd.gif)


Figure 3: Using an intermediary between the controller and the window.

The discussion so far suggests using Flow Synchronization with Supervising Controller, but this need not be the case. It is possible to use Observer Synchronization, but it needs to be modified so that it's the controllers that observe the model rather than the views.

迄今为止的讨论通常在监督式控制器中来使用流程同步，但这不是必须的，它也可以使用观察者同步，但是这个需要需改，以使得控制器监控model而不是view

When to Use It

There are two main reasons to look into using a Supervising Controller: to separate out the complexity of an Autonomous View, and to improve testability.

有两个主要的原因来使用这种模式

1.分离一个自治view

2.提高可测试性

The separation advantage is that it pulls all the behavioral complexity away from the basic window itself, making it easier to understand. This advantage is offset by the fact that the controller is still closely coupled to its screen, needing a pretty intimate knowledge of the details of the screen. In which case there is a real question mark over whether it's worth the effort of making it a separate object.

分离的好处在于可以把所有的负责的行为完全脱离view本身，并使其更加清晰易懂。这种优势通常会被控制器和视图紧耦合而抵消，所以需要一个相对亲密的屏幕细节知识。在每一个例子面前，分离一个对象，这通常都是一个问题

The testability reason is the more compelling one. Many people I've talked to have found using some form of controller has made a big difference to making a UI that is properly testable.

可测试性的原因通常是比较强迫的一个。一些我谈及的用户会使用一些格式的控制器，它们使得UI是可测试的一部分。

If testability is your driver then the driving issue is how much behavior to leave in the view. Passive View is a very similar pattern to Supervising Controller, but with the difference that Passive View puts all the view update behavior in the controller, including simple cases. This results in extra programming, but does mean that all the presentation behavior is testable. The choice between the two depends on what kind of Data Binding support you have and whether you're happy to leave that untested by controller tests.

如果可测试性是你的驱动，那么剩下的问题就是要多少行为脱离view。Passive View是一个非常相似Supervising Controller的模式。但是它们的区别在于Passive View把所有的view更新逻辑全部放到了控制器中，包括简单逻辑。这回导致冗余编程，但是这意味所有的展示逻辑可测试。在两者之间的选择取决于你有哪类的数据绑定支持还有就是你是否乐意留下一些无法测试的代码。

A third alternative is Presentation Model. This again pulls most behavior away from the view, but leaves it to the view to synchronize all its updates. There isn't a great deal of the difference in test coverage between Presentation Model and Supervising Controller - much of the choice (as with Passive View) depends on personal judgments.

第三条路就是Presentation Model。它把大部分的行为脱离view，但是遗留了更新逻辑。在测试复杂度上边，Presentation Model与Supervising Controller没有太多的区别。很多的选择取决于个人判断。
