title: Passive View
date: 2016-03-14 15:44:31
tags:
---
Passive View

A screen and components with all application specific behavior extracted into a controller so that the widgets have their state controlled entirely by controller.

伴随着所用应用特定行为的屏幕和他们的控件都被集中到控制器中，这样控件的状态完全由控制器操纵。

A perennial problem with building rich client systems is the complication of testing them. Most rich client frameworks were not built with automated testing in mind. Controlling these frameworks programaticly is often very difficult.

一个富客户端的建立的恒久问题就是如何去测试他们。许多框架并没有跟自动化测试在思想上统一。动态得操纵这些框架是非常困难的。

A Passive View handles this by reducing the behavior of the UI components to the absolute minimum by using a controller that not just handles responses to user events, but also does all the updating of the view. This allows testing to be focused on the controller with little risk of problems in the view.

Passive View是通过使用不仅仅处理用户事件，也负责更新view的控制器，减少UI控件的行为降低到比较低的水平。这种技术允许测试可以集中在控制器中，不用在view中冒风险。

How it Works

This pattern is yet another variation on model-view-controller and model-view-presenter. As with these the UI is split between a view that handles display and a controller that responds to user gestures. The significant change with Passive View is that the view is made completely passive and is no longer responsible for updating itself from the model. As a result all of the view logic is in the controller. As a result, there is no dependencies in either direction between the view and the model.

这种模式通常也是MVC和MVP的另一种变种。UI可以分为两部分，一个处理显示的view和一个处理用户手势响应的控制器。对于Passive View最显著的响应就是view完全是被动态的，它不再承担从model更新自己的职责。这样的话，所有的view逻辑全部集中在控制器中，这样的话，view和model中间再无瓜葛。

Figure 1

![image](http://www.martinfowler.com/eaaDev/passiveScreen/deps.gif)

Figure 1: Unlike most MVC-style configurations, Passive View results in no dependencies between view and model.

Looking at our regular assessment window example, again we see a view/controller split, but this time the controller does all the work in figuring out how the view should display the model. The text field widget receives the user gesture but immediately hands off to the controller, in the classic MVP movement. The controller then updates the model, and then handles the reloading of the view from the model. This load actions involves pulling all the data needed from the model, and using it to update the widgets. This example shows a coarse-grained synchronization, where any change results in a complete reload.

继续看我们例子，我们再一次将view和controller分开。但是这次控制器做了所有view该如何显示model的工作。文本输入框控件接收用户收拾然后第一时间就发送给控制器（这是传统MVP的流程）控制器接下来更新model，然后控制view从model中重新加载数据。这个读取动作包含
1.拉取所有需要的数据
2.用这些数据来更新控件
这个例子展示了比较粗粒度的同步，所有的改动都会导致完整的重加载。

Figure 2

![image](http://www.martinfowler.com/eaaDev/passiveScreen/assess-seq.gif)

Figure 2: When the actual text is edited, all the UI response is handled by the controller.

Figure 3

![image](http://www.martinfowler.com/eaaDev/passiveScreen/assess-cd.gif)


Figure 3: Classes for the assessment example.

The primary driver for Passive View is testing, as a result it's often valuable to use of Test Double for the view so that the controller can be tested without needing any interaction with the UI framework. This needs a intermediate Gateway to be setup as in Figure 4. This is the same technique that you would use for Supervising Controller. As with Supervising Controller I've shown a stub here, but this is also a good opportunity to use a mock.

使用Passview View 的原始冲动就是测试，这么做的结果就是使用Test Double通常是有价值的，因为控制器可以在不与任何UI框架交互的基础上测试。它也需要一个网关。
这项技术和之前Supervising Controller中使用的是一样的技术。

Figure 4

![image](http://www.martinfowler.com/eaaDev/passiveScreen/asssesIntermed-cd.gif)


Figure 4: Setting up the view as a Gateway so that it's easy to substitute the window with a Test Double

When to Use It

The driving reason to use Passive View is to enhance testability. With the view reduced to a dumb slave of the controller, you run little risk by not testing the view. The controller can run and be tested outside of the UI environment - indeed if you use a Test Double for the view you don't need to even have the UI classes available to you.

使用Passive View的直接驱使动力通常是加强可测试性。随着view降级为控制器的聋哑奴隶，不测试view，你将不会有很大的风险。控制器也可以在脱离UI环境下进行测试。事实是如果你使用Test Double，你甚至都不会使用UI类。

Passive View isn't the only way make the view sufficiently humble to help in testing. Presentation Model and Supervising Controller are both reasonable alternatives. The strength that Passive View has over these two is that both of the alternatives require the view to do some of the synchronization work, which results in more untestable behavior than Passive View. Whether this difference is significant or not is a judgment call.

Passview View并不是让view变的足够低级来帮助测试的唯一方法。Presentation Model和Supervising Controller都是比较合理的选项。Passview View强于另外两者的地方在于 另外两者需要view去做一些同步工作，他们会导致一些不太好测试的逻辑。至于这个区别是否很大就是一个判断题

Another advantage that Passive View is a very explicit mechanism. There's very little reliance on Observer mechanisms or declarative mappings. This makes it much easier to read the code to see what is happening - particularly useful if you're trying to debug when things go wrong.

Passive View 的另一个好处就是这是一个非常明确的机制。它并不依赖于观察者机制或者显式的绑定。它会是代码看起来更为清楚明了，尤其在事情进展不顺通过debug去解决问题时。
