title: Organizing Presentation Logic
date: 2016-04-05 15:54:31
tags:
---
#Organizing Presentation Logic

There are several ways to split up the logic of the presentation.

有好几种方法可以分离显示逻辑。

One of the most useful things you can do when designing any presentation layer is to enforce Separated Presentation. Once you've done this, the next step is to think about how the presentation logic itself is going to be organized. For a simple window, a single class may well suffice. But more complex logic leads to a wider range of breakdowns.

在设计任何显示层时一个最有用的事情就是强制实现分离。只要你这么做了，下一步要考虑的就是实现逻辑自身应该怎么组织。对于一个简单的视窗一个类足矣。但是更复杂的逻辑会导致更宽范围的故障。

The most common approach is to design one class for each window in the application. This class usually inherits from the GUI library's window class and includes all the code needed to handle that window. If a window contains a complex panel, you may have a separate class for that panel - leading to a composite structure. I'm not going to go into this kind of composite structuring, since that's pretty straightforward, instead I'll focus more on ways of organizing the basic behavior within a single window.

设计一个类最通常的方式就是为每一个窗口设计一个类。这个类通常需要继承GUI库中的视窗类并且包含所有处理视窗的代码。如果一个视窗包含复杂的面板，你可以需要为那个面板写专门的类，类似于组合模式。我们暂且不讨论这类组合结构，因为我们的例子很直接，我们会把精力放在一个视窗里基本行为的组织方法上。

##The Album List Running Example

For much of the discussion and examples here, I'm going to use a single example screen to discuss the issues that come up ( Figure 1). The window shows information about music recordings. For each album it displays the artist, title, whether it's a classical recording, and if it is classical: the composer. I chose the example to include some elements of presentation logic.

因为已经有了很多的讨论和例子，我会使用一个很简单的界面来讨论。这个视窗展现音乐唱片的信息。对于每个专辑，它会显示作者，标题，是否是传统唱片。如果是传统的，会显示作曲者。

The choice in the list selection determines which album's data is shown in the fields.
The window title is derived from the title of the currently displayed album.
The composer field is only enabled when the classical check box is checked.
The apply and cancel buttons are only enabled when data has been edited.


我选的这个例子会包含这些展现逻辑
1.当在左侧列表选择唱片后，它的数据会在右侧展现
2.视窗的标题会从当前显示的专辑中组合显示
3.作曲者文本框只有在经典选择框选中后才可用
4.应用和取消按钮只有在数据被编辑后才可用

Figure 1

 ![image](http://www.martinfowler.com/eaaDev/presentationModel/javaAlbum.jpg)


Figure 1: A simple album information window

##Separting presentation logic from the View

Although putting all the presentation logic in an Autonomous View is both common and workable, it does come with disadvantages. The most common disadvantage talked about these days with Autonomous View is to do with testing. Testing a presentation through a GUI window is often awkward, and in some cases impossible. You have to build some kind of UI driver that will drive the GUI. Some people use GUI tools that simulate raw mouse and keyboard events - but these tools usually create brittle tests that give false positives whenever minor changes occur to the presentation. More detailed tools address the UI controls more directly, these are less brittle but still troublesome. They also depend of the GUI framework having enough support for direct control access via an API - and not all do.

即使将所有的展现逻辑放到自治view中也是可以并且可行的，但是它仍有弱点。最大的弱点就是可测试性。通过用户界面测试通常是不方便的，在某些情形下甚至是不可能的。你不得不写很多能够驱使界面的测试用例。一些用户用GUI工具来模拟原始的鼠标和键盘事件。但是这些工具通常会产生这样的问题，当展现层有微小改动但是给反馈就不对了（严重依赖于页面）。更多的精准工具能够直接定位UI控件，它们可能不甚脆弱但是很麻烦。它们通常都依赖于GUI框架能够通过AOPI提供直接的控件访问，但不是所有的框架都有。


As a result many advocates of programmer based testing advocate a humble dialog (also known as an ultra-thin GUI). The core idea here is to make the class that contains the UI controls as small and stupid as possible by moving all the logic into other presentation layer classes. This GUI control class is usually referred to as the view, for reasons I'll get into shortly. You can then run your tests against the intelligent classes without needing to use any GUI controls, stubbing the humble view if necessary. Since the view is very stupid, there's little that can go wrong and you can find most bugs by working on the intelligent classes. A term I like to use for this style of testing is subcutaneous testing, since the tests operate just under the skin of the application.

所以 一些拥护GTD的程序员推崇对话框。这里核心论点就是使得类尽可能少包含界面控制，然后尽可能把所有逻辑挪到其他的显示层的类。界面控件类通畅都当view使用，原因我会简短说明。你可以针对那些类进行测试而不需要任何界面控制，如果需要的话可以用humble view打桩。因为view是非常简洁的，他们几乎不会出错，你可以通过在那些类上工作来发现大多数bug。对于这种测试，我喜欢用一个名词来表述，皮下测试。因为这些测试都是针对应用内部测试的。


Although subcutaneous testing is the primary reason these days for splitting a presentation class, there are a couple of other reasons why such split is worth considering. The intelligent classes can be made independent of several aspects of the view, such as the choice of controls, the layout of the controls, and potentially even the precise UI framework itself. This allows you to support multiple different views with the same logical behavior. Although this can be useful, supporting multiple 'skins' for a application, you can only do a limited set of variations by just replacing a humble view.

尽管皮下测试在这些日子里是分离实现逻辑的一个主要原因，这里还有一系列其他原因可以说明为什么分离展现逻辑值得认真思考。那些类可以独立于view的若干层面，譬如选择的控制，布局的控制 甚至可以精确到框架本身。它可以允许你去用同一份业户行为去支持多个view。通畅这些都是有用的，通过为一个应用支持多个皮，你可以仅仅通过替换个界面就可以变出一个变种的有限集合。

In some ways separating the presentation logic can make it easier to program the presentation. It allows you to ignore details of the view layout while you are writing your behavior, effectively giving you a more comfortable api to the view's controls. Stacked against this, however, is the fact that separating the presentation logic does result in extra machinery to support the separation (the nature of which varies depending on which pattern you use.) As a result there's a rational argument in both directions, saying separation either simplifies or adds complexity to a presentation.

通常而言，分离表现逻辑可以更容易的编程表现层。它会允许你在写行为的时候，忽略视图布局的细节，通过给你一个舒服的API，你就可以有效得去控制视图的控件。然而 还有一个事实，分离显示逻辑会导致更多的支持分离的机制。（差异的本质取决于你使用的模式）。对于这种分离，到底对于展现是简化了还是增加了复杂度，如今还有有争论。

There is a historical precedent for doing this split - it was part of the Model View Controller (MVC). As I discussed in [P of EAA], the MVC approach did two separations. The most important separation was Separated Presentation - separating the model from the view/controller. The other separation, that of the view and controller, was not popular in rich client GUI frameworks, although it did have a resurgence with web based user interfaces. In MVC the view was a simple display of information in the model and the controller handled the various user input events. This doesn't work well with most GUI frameworks because they are designed so that the UI controls both display and receive the user input events.

有好几种针对展现分离的历史先例。譬如MVC的部分理念。就像我在之前的讨论一样，MVC方法做了两种分离，最重要的分离就是展现分离，把model和view－controller对 分离。另一种分离指的就是view和controller，这个在客户端框架中不是很流行，即使基于网络应用中用户界面已经开始复兴。在MVC中，view只是简单的显示model中的信息，并且控制器来处理各式各样的用户输入事件。这个在大多数GUI框架中并不能工作良好因为他们设计的就是UI控件既接受用户输入事件，也负责显示。

To create a humble view, the design must move all behavior out of the view - both handling of user events and any display logic of domain information. There are two main ways of doing this. First is the the Model-View-Presenter style where behavior is moved into a presenter, which you can think of as a form of controller. The presenter handles user events and also has some role in updating the view. Supervising Controller and Passive View are two styles of this approach. Supervising Controller puts simple view logic in the view, while Passive View puts all view logic in the controller. The alternative style is Presentation Model which creates a form of model that captures all the data and behavior so that the view only needs a simple synchronization.

如果建立一个humble view，这种设计必须要把所有逻辑挪出视图，既包括用户事件的处理也包括领域信息的显示逻辑。这里有两种方式可以达成这件事。第一就是用MVP。把行为挪至presenter中，它其实是另一种控制器的方式。presenter处理用户事件，也有若干更新视图的角色。 Supervising Controller和Passive View是这种方式的两种具体实习办法。Supervising Controller在视图中放置极简视图逻辑，Passive View把所有视图逻辑放进控制器。另一种变种就是Presentation Model，它会抓取所有数据和行为，view仅仅需要简单的同步。

In both styles the view is the initial handler of user events but then immediately hands off control to the controller.

在所有的变种中，view都是初始用户事件的处理者，但是紧接着他们马上就交出控制权给控制器。

All three of these patterns, by introducing an extra class, produce a arguably more complex design. Splitting a class that does too much into separate classes to fulfil each responsibility is a good practice - but the question is whether an Autonomous View is too complex. Certainly the other patterns provide other testing options and the ability to support multiple views. If you don't need the multiple views and are happy with testing through the view then Autonomous View may well be fine, particularly if the window isn't too complex.

所有的这三种模式，通过介绍用多余的类，来产生一种可论证的比较复杂的设计。把一个承载了太多只能的类拆分成若干个类，每个类只负责其中的一部分是一个很不错的实践。但是现在的问题时一个自治view是否是太复杂。诚然其他的模式提供其他的测试选项，也能支持多视图。如果不需要多重视图并且也非常乐意通过view去测试，那么自治view也很好，特别是在窗口不复杂的情况下。

The choice between Presentation Model, Supervising Controller and Passive View is more arbitrary - it really comes down to how easy it is to do the pattern in your GUI environment and on your own personal tastes.

这三种方式的选择可能比较武断，它需要你看看在自己的用户界面环境集成这个有多方便。

Whenever we use terms from MVC there's inevitably the question of what is the model. In classic Smalltalk MVC the model was a Domain Model. In general the model in an MVC discussion these days means the interface to the domain layer; which could be a classic domain model, or a Service Layer, Transaction Scripts, Table Modules or any other representation of the domain. Indeed if there is no separate domain layer the model could well be the interface to a database.

当我们使用MVC的时候，有个不可避免的问题，什么是model。在经典MVC中，model就是领域模型。通常而言，model在MVC的讨论经常意味着时对领域层的接口。它可以是一个经典的领域模型，或者一个服务层，交易脚本，表模块或者其他的领域展现。实际上如果没有单独的领域层，model也可以作为数据库的接口。



##Screens, Layers, and Data

Most enterprise applications involve editing data. This data is usually copied between several layers of the application, and probably between multiple users using the same system. Much of the behavior of an enterprise application depends on how the changes of this data are coordinated and how the data is synchronized between the layers. There's no generally accepted terminology for how to think about this, for the purpose of this book I'm going to impose the following.

大多数企业应用都包括编辑数据。这个数据通常应该在应用的几个层次之间进行复制，也有可能多用户之间使用相同的系统。太多的企业应用的行为依赖于如何协调这些数据的变化，如何在多层之间进行同步。对于这个问题，没有太多被广泛接受的专业术语，为了本书叙述方便，我引入了接下来的词语。

###Layers of Data

数据层

The first issue to think about is the different copies of the data in different layers. Thinking about this in physical terms there's the usually difference between data in memory and data in a database or files. You can think of this as the difference between transient and persistant data. However I find it's usually a bit more than this. Even data in memory is often in two places. Often you'll find a difference between data on a screen and data in some form of in memory store that backs a screen. This could be in a Record Set retrieved from a database (but not yet committed back to the database), or in a Domain Model.

第一个要考虑的是说在不同层的数据的不同类型的拷贝。在物理层面上，他们通常是不同的，譬如在内存中，在数据库里，在文件中。你可以认为这个是长久和临时数据的区别。然而我发现它通常不止于此，即使在内存中的数据也会通常在两个地方，你会发现在前台时的数据和在后台时内存存储的数据之间的区别。他们通常在一个从数据库里返回的数据集（并且还没有最终提交到数据库中）或者是一个领域模型。

Consider working on a document in in a word processor. The document is on disk and you've opened the document in the word processor and edited the text of the document. This yields different text in memory than from on disk. Now open a dialog box to change the formatting for some of the text. Often you can change the formatting in the dialog box but it doesn't change the underlying text until you hit an apply button. The formatting data in the dialog box is in-memory data, but it's different data to that in the main in-memory document.

考虑在文本处理器中处理一个文本。这个文档在硬盘中，你可以用软件打开它并且编辑里边的文字，它会在内存中产生不同的文本 （与硬盘不同）这个时候打开对话框来更改一些文本的格式。通常而言你可以更改对话框中的格式但是它不能改变潜在的文字，除非你应用修改。这个时候，在对话框中格式化的数据也是内存数据，但是它跟同样在内存中的文档有很大的不同。

The terminology I shall use in this book is screen state, session state, and record state. Screen State is data displayed on a user interface. Session State is data that the user is currently working on. Session state is seen by the user as somewhat temporary, they usually have the ability to save or discard their work. Record State refers to more permanent data, data that's expected to sit around between the sessions.

我将要在这本书中使用的名词就是screen state， session state， record state。Screen State是显示在用户界面的数据，Session State是用户正在与之交互的数据，Session state对于用户而言看见得可能有点短暂，他们通常有能力保存或者丢弃。Record State通常是指持久数据，这个数据可以在不同的会话层中存在。

Session state is primarily acted on in-memory, but is often stored to disk. Modern word processors often save rescue files to avoid losing work due to power loss or a system crash. An enterprise application can save session state in a checkpoint file locally or the server may save state to disk between requests.

会话层状态首要会存在在内存中，但是通常会存储到硬盘中。现代文本处理器通常会存储救援文件来因断电或者系统挂掉导致的工作丢失。一个企业应用可以存储会话状态到一个本地的检查点文件，或者服务器会在请求之间将其存储到硬盘中。

In an enterprise application a particular distinction between session and record state is that record state is shared between the multiple users of a system, while the session state is private state visible only to user working on it. So not just is the user deciding to save changes to a more permanent form, they are also deciding to share it with their co-workers. Session state often correlates with a single business transaction, although it often spans multiple system transactions, a situation that often requires offline concurrency.

在一个企业级应用中，会话层和记录层的一个显著区别就是记录层是被多用户系统共享的，而会话层数据是私有的仅对当前工作的用户有效。所以用户不仅可以决定是否存储保存的问题，它们也通常会决定与其他同事共享。会话层经常与一个业务交易相关联，及时它经常跨越几个系统交易，一个解决方法就是经常要求线下并发。

Not all applications have session state. Some just have screen state and record state - any change to the data when saved goes directly to the record state. In these cases you may not have any session state at all, or any changes are written immediatly to record state so the session state is always in lock step with the record state. Not having session state greatly simplifies and application as you then don't have to worry about managing the session state. It's even preferred by users in many applications since the user never needs to worry about losing work. However leaving out session state isn't all strawberries. The user looses the ability to play around with a scenario in working state and then discarding it if they don't like it. It also stops people working independently on a multi-user application.

但并不是所有的应用都有会话层。一些仅仅有用户界面层和记录层，所有对于数据的改变都会在保存时直接存入记录层中。在这些情形中你可能不需要会话层，或者所有的改变直接立即写入到记录层这样会话层通常会处于锁定状态。不持有会话层可以最大化地减化应用，所以你不用担心管理会话层的状态。在某些应用中这甚至是受欢迎的，因为用户始终不用担心会丢失工作。但是不使用会话层也并不是全无陷阱，用户失去了一个在工作状态下玩转一个场景的能力，如果在他们不喜欢他们的时候可以随时丢弃。它同时也阻止了用户独立地工作在一个多用户应用中。

You can get extra layers of state too. An example of this is when session state is stored on both a client tier and a server tier. These states can change independently, although there are usually fairly restrictive rules about how they syncrhonize which makes managing them much simpler.

你可以得到额外的层。这里的例子就是会话层存储在client－server tier中。这些状态可以独立地去改变，即使他们通常在同步高效性上有相当的限制

An example of extra layers would be how a developer works on a team. In this case the record state is the state of the shared source code repository. The working copy on the developer's local machine is a form of session state, sitting on the disk. In this case it's somewhat transient itself. There are then other representations in the IDE. In a modern IDE that holds and constantly updates an syntax tree in memory you have that, plus the text shown on the screen. In this case there more than three layers, but it's still useful to think about what the developer sees on the screen, his private session data, and the shared permenant data. To reason about effectively I would name each set of data and talk about them as separate layers. Particular applications will always have their own set, for my discussion I'll focus on the common triad of screen, session, and record state.

额外层的例子通常意味着一个开发者如何工作在一个团队中。在这个例子中，记录层是一个可共享的源代码仓库。在开发者本地机器上的工作拷贝是会话层，存储在硬盘中。在这里它通常是比较短暂的。IDE有很多的不同的发型版。在一个现代的IDE中，通常会持有和持续更新你在内存中的语法树，并把它显示在屏幕上。在这里，可能不止三层，但是它仍然对于开发者思考什么是屏幕看到的东西，什么是他私人会话数据，以及是什么是它共享的持久数据。为了更有效地阐述这个问题，我命名了三类数据并把他们分了不同层，不过有的应用把他们都合三为一，为了我们的讨论，我将焦距在这三个状态。

Most of the time a user works in a single session at one time. On occasion a user will operate in multiple sessions at once. This often causes confusion because changes in one session won't show up in another session until both are synchronized with record state. You can get around this by synchronizing the two sessions, but it's usually messy.

大部分时间用户都会在同一时间内工作在一个会话层中。操作多个会话层应该是偶然情况。这样通常会导致困惑，因为在一个会话层的改变不应该呈现在另一个会话层中直到他们都与记录层同步。你可以选择同步两个会话层，但是那个通常比较复杂。

These multiple states often correspond to the various layers of an enterprise application. In an ideal application that uses presentation, domain, and data source layers you'll have domain logic only operating on session state. In practice this distinction gets muddied, usually for bad reasons but sometimes for a good reason. In an application where the domain layer is on a separate process to the presentation you may want some domain logic running in the presentation process to make the application properly responsive. Such logic may involing copying some session state from the main domain process, or you may have to run domain logic against the data in the presentation's controls. Similarly if you need to operate on large amounts of data you may need to embed domain logic into a database through something like stored procedures. Such logic operates on record state. However most of the time you want domain logic to operate on session state.

多个状态通常会对应一个企业应用的多个层。在一个理想的应用中通常使用表现 领域 数据层。你可以让领域逻辑仅仅在会话层中工作。在实践中，这个区别可能比较葛，有时候是好事有时候不好。在一个应用中 领域层通常会独立存在一个进程中，你可能在表现层进程中有一些领域逻辑来使得应用更好地响应。这些逻辑包括从领域进程中拷贝会话状态活着你想在表现层控件中运行领域逻辑。相似的 如果你需要操作大量的数据 你需要嵌入到领域逻辑通过一些存储过程到数据库中。这些逻辑在记录层操作。但是大多数时间你可能希望在会话层中操作领域逻辑。

###Synchronizing Between Layers

Synchronizing data between these different contexts is an important part of building an application. When you're working on a user interface you can synchronize the screen state to two different depths: the session state or the record state. If you synchronize to session state you'll need some control that allows the user to save the session state to the record state.

在不同的上下文总同步数据是建立一个应用的重要组成部分。当你在用户界面前时你可以同步屏幕数据到两个不同的深度，一个是会话层，一个是记录层。如果你同步到会话层，你需要一些控件来允许用户保存会话层到记录层。

Synchronization can occur at various frequencies, I find the following three useful. Key Synchronization means you synchronize on each key press or mouse click. Field synchonization means you synchonize when you finish editing a field. Screen synchronization means you synchronize when you some special buttons in the UI (usually labelled 'apply', 'OK', 'cancel', or 'submit') when you are done with a screenful of information.

同步可以发生在任何时候。我找了以下三种同步。
1.键盘同步 意味着每点击按键 每点击鼠标 你都可以同步
2.域同步 当你完成编辑框时同步
3.屏幕同步 一些positive和nagetive操作时同步

Once you need to synchronize, the next question is how much do you synchronize. When you're looking at synchronizing screen data with session data then I see two main schemes. Coarse-grained synchronization implies that whenever you change anything on the UI, the entire UI is synchronized; so changing the artist field means synchronizing the entire window even though nothing else needs to change. Fine-grained synchronization means only changing the fields that really need updating. So changing the title field would involve synchronizing the title field, window title, and list box - but nothing else.

一旦你需要同步，下一个问题就是你要多少同步。
当你查找同步屏幕状态到会话层时，我看到了两种策略。
1.粗粒度  当你更改了UI上的任何东西 整个UI同步
这就意味着如果修改作家域就要整个窗口都刷新，即使它根本没必要刷。
2.细粒度  只刷该刷的。所以更改标题域 应该同步标题，窗口标题和列表。

Synchronizing between the session data and record data usually uses a different approach. Session data isn't usually used by multiple people simultaneously, so you don't have to worry about concurrency issues. Syncrhonizing between session and record data usually occurs with screen synchronization and usually takes longer. As a result you do such things as mark data elements as dirty or use a Unit of Work.

同步会话状态和记录状态通常需要不同的方法。
会话层数据不能被多个用户同时使用，索尼不用担心并发的问题。
这两个状态间的同步通常伴随着屏幕状态的同步，并且时间都会长一点。
结果你可以标记某些数据是脏数据活着用工作集。

All of these aspects trade off with each other in both the internal design and the interaction design of the UI. The most obvious trade-offs occur between the frequency and depth of synchronization. Using key synchronization down to record state would lead to unnacceptable performance, amongst other ills. As a result most of the time I see screen synchronization used at that depth. Indeed screen synchronization is also the most common at session depth too. It's usually the easiest to do and many applications work that way so users are used to it. However it's also quite common that the interaction design really needs field synchronization. Field synchronization is pretty easy if the domain logic is in the same process as the presentation, it's rather more awkward to get good performance if it's in a different process. So for the domain layer, screen synchronization is a reasonable default, but expect to do field synchronization fairly often.

在内部设计和内部界面的设计 所有的因素都需要权衡。最明显的权衡发生在同步的频度和层次。用按键同步的方式可能会导致无法接受的性能。对于频率而言，我大部分时间内见的比较多的是屏幕同步。实际上屏幕同步的深度通常也是到会话层。这个同步通常非常容易做，并且被多数应用采用。但是有一些交互设计就要求域同步。域同步也比较简单，如果域逻辑与显示逻辑在同一个进程中。如果不在同一个进程中，可能获得好的性能就比较别扭。对于领域层，屏幕同步是比较合理的，域同步也比较常见。

Key synchronization seems to be rarer, but is pretty easy to do if the domain is in the same process.

按键同步看起来比较稀少，但是如果在同一个进程中的话比较简单。

While timing choices vary with depth and application design, I'm almost always in favor of coarse-grained synchronization between screen and session states. Many people shy away from coarse-grained synchronization because they're concerned with the performance implications. But fine-grained synchronization is a bear to maintain since there's lots of code with frequent duplication. Bugs in all this are hard to spot and thus fix. Most of the time coarse-grained synchronization is quite performant enough, so my advice is to always use it first. If you do run into performance issues, and you've profiled to check that it really is the synchronization, then you must introduce a little fine-grained synchronization to fix it. At that point do the minimum you need to do to deal with the performance issue.

候选的这些选择主要在应用设计和层级有所不同，我比较钟情从屏幕到会话层的粗粒度同步。许多人不想用粗粒度主要是有性能担忧。但是细粒度同步维护起来压力较大，因为有大量代码经常性copy，很难指出并修改。
大多数时候 粗粒度同步性能是够的 所以我的建议就是先优先使用它。如果你更关注性能问题，你可以查查这里到底需不需要同步，然后用细粒度的方式修改。在这个节点上，做得尽可能少来解决你的性能问题。

This need for synchronization is such a common one, that it's inevitable that people develop frameworks to try to handle it. One that gets a lot of attention is the data binding framework in .NET, which autmatically synchronizes screen and session state. Data binding has many nice qualities and in theory should be able handle synchronization well. So far (up to version 1.0) I've found that it works well for simple cases but breaks down in moderately complex ones. Projects I've talked who started with data binding ended up dropping after a while because there weren't enough ways to control how the binding was working. As a result I would advise treating it warily unless your needs are very simple. However do re-evaluate it in later versions - I could easily see this turning into a very effective solution to synchronization issues.

同步的需要是个普遍的问题，用户开发框架时会不可避免地来处理这个问题。
一个是.net的databinding框架，自动同步屏幕和会话状态。数据绑定有着不错的质量并且在理论上能够很好得处理同步。



###Synchronization and Multiple Screens

One part of synchronization is about synchronizing between the layers of state, another part is dealing with synchronizing between multiple strands of the same layer. You'll often find mutliple sessions on top of a single record state, and multiple screens on top of each session. Each of these is a separate context, and you have to think about how changes in one context are propagated into other contexts.

同步还有一部分应该是层之间的状态同步，另一方方面就是处理多个搁浅的相同层之间的同步。你有时候会发现多个会话层在一个记录层上方，多个屏幕层在每一个会话层上方。每一个都是一个单独的上下文，你不得不思考在一个上下文的变化怎么表现在其他的上下文中。

Since I'm talking about presentations here, I'm not going to say much about synchronizing multiple sessions. In any case that's a better understood and relatively straightforward topic. Most of the time sessions are isolated from each other and only syncrhonize with record state. They use transactions or some form of offline concurrency control to do that.

因为我现在在谈论展现，我不打算说太多多个会话层的同步。在任何情形下，有更好的理解和相对比较直白的文章可以参考。大多数时间，会话是相互隔离的，并且仅跟记录层同步。它们利用交易或者某种形式的离线并发控件来做这个事情。

Presentations are more complex because users expect less isolation and more rapid synchronization.

展现比较复杂因为用户期盼较少的隔离和更快速的同步。

How best to synchronize multiple screens depends very much on the way screens are organized and on how the flow between screens is structured. To look at it from two extremes, we can think about contrasting wizards with a completely non-modal interface such as a file system explorer.

怎样更好地同步多个screen，它们可能依赖很多，它们怎么组织，或者是这个流程应该怎么架构。从两个极端考虑这个问题，我能够认为文件系统浏览器是一个完整的非模态界面。

With a wizard user-interface, the system guides the user through a very controlled flow of screens. At any time only one screen is visible and usually the user can only go backwards or forwards from each screen. In this situation the designer of the screen knows exactly what data is displayed and exactly when screens are opened and closed.

通过一个智能的用户界面，系统能够通过一个可定制化的界面来引导用户。任何时候只有一个界面可见并且用户通常只能在每个界面中前进或者后退。在这种情形下，界面的设计者必须清晰地知道什么数据将要被显示，界面将在什么时候打开或关闭。

With a file system explorer the user can move around between screens at will. More importantly the user can open up multiple explorer windows showing the same files. If a user changes the name of a folder in one window, other windows should be updated too. The programmer of the UI is never really sure when windows will be opened and whether the same data is displayed in multiple windows or not.

通过一个文件系统浏览器，用户可以在界面中来回切换。更重要的事用户可以打开多个浏览器来显示相同的文件。如果一个用户在一个窗口里更改了文件夹的名字，那么其他窗口也应该更新。界面的编程者永远不确认视窗什么时候被打开，并且相同的数据是否要显示在多窗口中。

These two extremes suggest two different ways of coordinating information between screens. With Flow Synchronization each screen decides when to synchronize its screen state with any underlying session state, based on the flow of the application. So with a wizard, the screens would typically syncrhonize when moving from one screen to another; writing out the old screen and reading in data for the new one. Flow Synchronization works best when the flow between screens is simple and there are clear points at which you can save and load data from screen state into session state.

这里有两种截然相反的协调多个界面之间信息的方式。通过流同步，每个界面根据应用程序的流程，自己来决定什么时候与潜在的会话状态同步。所以通过一个向导，界面通常在切换时同步，将新状态写入并且让新界面从中读取数据。流同步在界面之间切换简单并且存储读取界面状态时间点明确的情形下使用，效果比较好。

For a file explorer Flow Synchronization would be difficult. One screen can never really tell whether another screen has changed the underlying data. In this case the screens need to be unaware of each other, and synchronize whenever the underlying data changes. With Observer Synchronization the underlying screen state acts as the master source of the data. Whenever it changes, the screens that display are notified and can update their screen state, usually using the Observer pattern. In this form Observer Synchronization is a fundamental part of the Model View Controller style.

对于文件浏览器而言，流程同步可能比较困难。一个屏幕基本不会告诉另一个界面是否改变了数据。在这个情形下，界面需要相互之间无关，只在后台数据变化时同步。在观察者同步这种机制下，潜在的界面状态扮演着数据的主要来源，无论它什么时候改变，显示界面都会得到通知，并且更新他们的屏幕状态，这通常就是观察者模式。观察者同步通常是MVC模式的重要部分。

The nice thing about Observer Synchronization is that all the screens are always completely independent of each other, both in that they don't need to know about each other to synchronize and that they don't need to tell each other about synchronization events. This makes it easy to have very ad-hoc and complex flows in the application. The downside of Observer Synchronization is that it relies on using Observer and that introduces some implicit behavior that can get very tricky if you let it go out of control.

观察者同步比较好的地方在于所有的界面之间是相互独立的。它们不需要知道它们之间的同步信息，它们也不需要相互告诉对方同步事件。这使得那些临时和复杂的流程变得很容易构建。观察者模式的缺点在于它依赖于观察者，有些潜在的行为在失去控制的时候变得很微妙。

On the whole, however, Observer Synchronization is the dominant choice for complex UIs. Flow Synchronization is really only usuable if the application flow is very simple: usually one active screen at a time and simple flows between screens. Even then, once you are used to Observer Synchronization you may prefer to use it even for these simple cases.

然而从整体而言，观察者模式对于复杂的界面而言是个多数选择。流同步通常只在应用流程非常简单时有用。通常是只有一个活动页面并且页面之间的流程很简单。但是现在，如果你之前用过观察者同步，你可能针对简单情形也想用它。

##Observer Gotchas

Many interactions in a rich client presentation make use of the Observer pattern. Observer is a useful pattern, but it comes with some important issues that you need to be aware of.

在一个富客户端中一些交互全部使用观察者模式。观察者模式是一种非常有用的模式，但是它会带来一些你需要注意的比较重要的问题。

The great strength, and weakness, of observer is that control passes from the subject to the observer implicitly. You can't tell by reading code that an observer is going to fire, the only way you can see what's happening is to use a debugger. As a result of a complex chain of observers can be a nightmare to figure out, change, or debug as actions trigger other actions with little indication why. As a result I strongly recommend that you use observers only in a very simple manner.

观察者模式最强的地方最弱的地方就是从subject到observer的操控隐蔽性。你不能通过阅读代码来看到观察者是怎么被激发的，你只能通过调试器来查看这些是怎么发生的。如果有一连串复杂的观察者链，那么指出，改变，调试一个行为时怎么触发其他行为简直是场噩梦。所以我强烈建议在简单的情形下使用观察者。


Don't have chains of objects observing other objects, observing other objects. One layer of observer relationships is best (unless you use an Event Aggregator
Don't have observer relationships between objects in the same layer. Domain objects shouldn't observe other domain objects, presentations should not observer other presentations. Observers are best use across the layer boundary, the classic use is for presnetations to observer the domain.

千万不要整观察者链，一层观察者的关系就是最好的，除非你使用事件集合器。不要在同一层中使用观察者模式，领域对象不应该观察领域对象，展现对象不应该观察展现对象。观察者最好在层边界中使用。最通常的使用方式就是展现层观察领域层。

Another issue for observers lies in memory management. Assume we have some screens observing some domain objects. Once we close a screen we want it to be deleted, but the domain objects actually carry a reference to the screen though the observer relationship. In a memory-managed environment long lived domain objects can hold onto a lot of zombie screens, resulting in a significant memory leak. So it's important for observers to de-register from their subjects when you want them to be deleted.

另一个观察者模式的问题就是内存管理。假想我们有多个界面观察着一些领域对象。如果我们关闭了一个我们将要删除的界面，但是领域对象通常会通过观察者关系持有一个界面的引用。在内存管理环境中，一个长时间存活的领域对象可能会持有大量的僵尸界面，可能会导致巨大的内存泄漏。所以对于观察者而言在它们将要被删除时，一定要先删除然后重注册。


A similar issue often occurs when you want to delete the domain object. If you rely on breaking all the links between the domain objects this may not be enough since screens may be observing the domain. In practice this turns out be a problem less frequently as the screen departs and the domain objects lifetime are usually controlled through the data source layer. But in general it's worth keeping in mind that observer reltionships often hang around forgotton and a frequent cause of zombies. Using an Event Aggregator will often simplify these relationships - while not a cure it can make life easier.

一个相似的问题会发生在你想删除一个领域对象。如果你想打破领域对象之间的联系可能没那么麻烦，因为界面可能会观察着领域对象。在实践中，这证明是个问题 领域对象的生命周期通常被数据层控制， 如果将界面独立的话 会减少很多问题。通常而言一定要记在心中，观察者关系会在遗忘中，也可能会导致一堆僵尸，用事件结合器会简化这种联系，但并不是让生活变得容易的解药，你懂的。
