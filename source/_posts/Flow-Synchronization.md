title: Flow Synchronization
date: 2016-04-05 15:52:57
tags:
---
#Flow Synchronization

Synchronize screens with an underlying model based on the flow of the user interaction between the screens.

通过用户在界面之间的操作流程来同步潜在的model数据到界面。

Applications often contain multiple screens that display the same data. If the data is edited in one screen, then the other screens must be updated to reflect the changes.

应用通常会包含好多显示数据的界面，如果数据在一个界面里编辑了，那么其他界面应该响应这种变化。

Flow Synchronization does this by having the screen code explicitly re-synchronize at the appropriate point in the work flow of the screen.

流程同步就是通过在用户操作界面整个流过程中间的某个合适的时间点显式地同步来完成数据的同步。

##How it Works

Flow Synchronization is a very simple approach to explain. Essentially each time you do something that changes state that's shared across multiple screens, you tell each screen to update itself. The problem is that this means, in general, that every screen is somewhat coupled to the other the other screens in the application. If you have a lot of screens that are working in a very open-ended way, this can be nasty - which is Observer Synchronization is such a strong alternative.

流同步是一种非常容易解释的方法。基本上每次你做被多个界面共享的状态修改时，你应该告诉每个界面让它们刷新自己。这个问题通常而言就是每个界面通常会跟其他界面解耦。如果已经有了很多界面工作这种开放状态，这会很让人难受，这个时候观察者同步是更好的方法。

Flow Synchronization thus better with simple navigation styles.

流同步最好应用于简单的导航模式。

One common style is a root and child style. Here you have a root window that is open for the whole application. When you want more detailed information you open a child window, which is often modal. In this situation it's easy to use Flow Synchronization, all you need to do is to update the root whenever you close a child. If you do this in the root screen the child doesn't need to be aware of the root.

一个比较常见的模式是根与孩子模式。假设这里你有一个整个应用开放的根视图，当你想要一个更为详细的细节信息，你可以打开子窗口，它经常是模态的。在这种情形下，应用流同步非常容易，你要做的就是当你关闭子窗口时创新根视图。如果你在根视图这么做了，则子窗口根本不用关心根视图。

If the child is non-modal, this can get become more difficult. It's straightforward if you follow the approach of only updating the data (and thus the root) when the child is closed. If you want updates while the child window is still open then you are moving towards the territory of Observer Synchronization. It's also problematic if data is shared between the children.

如果子窗口不是模态的，这个可能会变得比较困难。当子窗口关闭时，仅仅更新数据的方法很直接。如果你想在子窗口尚在打开的情况下更新那你就可以使用观察者更新。这个方法在多子窗口共享数据时有问题。

Another simple style is a wizard style where you have a sequence of screens in order. With a wizard each screen is modal and movement from one screen to another involved closing the old screen and opening another. So in this situation Flow Synchronization is easy to do: update the data on closing a screen and load the fresh data when opening the next screen. You can have a wizard sequence of modal children with a root screen where closing the last child updates the root.

另外一种方式就是 导航模式 （有一系列有序的界面）。在导航模式中，每个界面都是模态的并且从一个界面到另一个界面时是关掉旧的打开新的。所以在这种情形下，流同步很容易做。在关闭界面时更新数据，在打开下一个新的页面时刷新数据。你可以有一个根窗口带着一系列模态子序列，当关闭最后一个子窗口时更新根视图。

##When to Use It

Flow Synchronization is an alternative to Observer Synchronization for synchronizing screens with domain data. It is, in many ways a more explicit and straightforward way of doing things. Instead of implicit Observer relationships which can be hard to see and debug, you have explict synchronization calls clearly laid out in the code.

流同步是同步领域数据到界面的观察者同步的另一种方式。这是一种比较直截了当的同步方式。取代了难以看到和调试的隐性的观察者关系，你可以在代码中有非常清晰明了的同步调用。

The problem with Flow Synchronization is that things can get very messy once you have an unconstrained amount of screens that may share data. Any change on any screen needs to trigger other screens to update. Doing this through explicit calls to other screens makes screens very interdependent. This is where Observer Synchronization is so much easier.

流同步的问题主要在于你如果有不可限制数量的界面共享数据时，它会非常麻烦。所有界面中的改变会导致其他界面的更新。通过这种显示调用让其他界面更新的方式会造成代码紧耦合。这里使用观察者同步就会很简单。

Despite its limitations, Flow Synchronization does have its place. It works well providing the navigational flow of the user interface is simple and only one or two screen are active at the same time. Examples of such situations are sequences of screens (such as wizards) and a root screen with modal children. Observer Synchronization will also work with these cases but you may find the more explicit approach preferable. Web user interfaces are effectively a sequence of screens and thus work effectively with Flow Synchronization, this is an example where the protocol of the client/server connection makes Observer Synchronization very difficult to do.

尽管有这个限制，流同步还是有它的用武之地。它会在提供给用户的界面操作流程极简和仅仅有一两个页面同时显示的情况下工作良好。类似于导航界面和有若干个模态子窗口的根窗口。观察者更新也可以在这些情形下工作良好，但是你可能需要一种更为清晰明了的同步方式。web用户界面一般由多个界面组成，一般用流同步工作良好。CS端使用协议通信会导致观察者同步非常难做。

Example: Root and Child Restaurent List (Java)

With example I hava couple of simple screens. The root is a list of restaurents. To edit the list you click on the restaurent entry to edit the details. If you change the name, then the list needs to update itself, both in the contents of the name and also to preserve alphabetical order.

通过这个例子，我拥有很多界面。根窗口时一个饭店列表，点击其中一项可以编辑里边的细节。如果更改了名字，列表也需要自更新，并且内容里的名字也得修改并保持字母序列。

The restaurent class is so boring I won't repeat it here. There are just string fields corresponding to the fields on the UI and the attendent getters and setters. Not much of a class, but it's not the focus of this discussion so I can permit its sad, pathetic existance.

参观类是如此的烦人以至于我不想重复它。它仅仅有对应字段的几个string域和附带的get set方法。但是它不是我讨论关注的焦点，暂且允许它存在。

The form that edits the restaurent details is also rather simple. I have a single flobal update listener that I attach to each field, so any change in a field causes a global refresh of the underlying domain object and the form - this is coarse-grained synchronization.

更改参观细节的表格也相当简单。我有一个为所有field加的全局的更新监听器，所以任何界面的修改都会导致全局的领域对象的刷新，这是一种粗粒度同步。

class RestaurentForm...
  public class FieldListener extends GlobalListener {
     void update() {
         save();
         load();
     }
  }
  private void load() {
      nameField.setText(model.getName());
      placeField.setText(model.getPlace());
      favoritesField.setText(model.getFavorites());
      directionsField.setText(model.getDirections());
  }
   private void save() {
       model.setName(nameField.getText());
       model.setPlace(placeField.getText());
       model.setFavorites(favoritesField.getText());
       model.setDirections(directionsField.getText());
   }
The restaurent list needs to be updated at two points, on initial creation of the form and whenever a child window closes. I put the code to update the list in a method.

餐馆列表有两个时机需要更新
1.初始化创建时
2.任何时候子窗口关闭时

class RestaurentListForm...
  void load() {
      sortModel();
      list.setListData(getRestaurentNames());
  }

  private String[] getRestaurentNames() {
      String[] result = new String[model.size()];
      int i = 0;
      for (Restaurent r : model) result[i++] = r.getName();
      return result;
  }

  private void sortModel() {
      Collections.sort(model, new Comparator<Restaurent>() {
          public int compare(Restaurent r1, Restaurent r2) {
              return r1.getName().compareTo(r2.getName());
          }
      });
  }
I then call it from the constructor of the form and from an action listener attached to the edit button.

这样我们可以在点击编辑按钮时绑定一个事件监听器。
在这个监听器和构造函数里加载数据。

class RestaurentListForm...
  public RestaurentListForm(List<Restaurent> model) {
      this.model = model;
      buildForm();
      load();
      window.pack();
      window.setVisible(true);
  }
private class EditActionListener implements ActionListener {
    public void actionPerformed(ActionEvent e) {
        new RestaurentForm(window, selectedRestaurent());
        load();
    }
}

##Comparing to a custom List Model

An obvious alternative to this approach is to use a custom list model. The implementation above uses a built in list model which takes a copy of the data from my list of restaurents. An alternative to a copy is to provide my own list model implementation that is just a wrapper on the underlying list of restaurents.

这种方式的另一个变种就是使用自定义model，上边的实现使用了系统自带的model来接受我们的餐馆列表的拷贝。另一种方式是直接提供我们自己的model实现，它仅仅是封装下数据。

private class RestaurentListModel extends AbstractListModel {
    private List<Restaurent> data;
    public RestaurentListModel(List<Restaurent> data) {
        this.data = data;
    }
    public int getSize() {
        return data.size();
    }
    public Object getElementAt(int index) {
        return data.get(index).getName();
    }
}
Now I create this wrapper on the list of restaurents and supply it to the list widget when I create it. This way the list uses the underlying list of restaurents as its model, rather than a separate list of strings. Any changes I make to the restaurent name is automatically propagated through to the screen, so I don't have to write or call the load method.

现在我建立了一个餐馆列表的封装器并且在创建时提供给列表控件。这样列表控件就可以使用这个作为它的mobel，而不是一个分散的字符串列表。所有针对于餐馆名的修改都会自动在界面里展示，所以我不需要写或者调用加载方法。

A more tricky question is what to do about the sort order of the list. If the sort order makes sense for the domain classes, then I can simply sort them there, as long as I re-sort the restaurent list when I change a name. Often, however, sort orders are particular to a screen, in that different screens may have different sort orders. In this case triggering the sort scheme can be more of a problem. An alternative is to sort the list each time it's asked for - but that may make the UI unresponsive, particularly if the list is large.

一个更好的问题时如何去做列表的排序。如果排序对于领域类说得通，我就能在这里简化排序。只要我修改名字后就重新排序。然而 排序一般是特指某个界面的，每个界面可能有不同的排序方式。在这种情况下，触发排序逻辑是个问题。一种替代方案时每次请求时排一次序，但它可能在列表过大的时候造成UI无响应。
