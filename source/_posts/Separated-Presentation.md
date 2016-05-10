title: Separated Presentation
date: 2016-04-05 15:55:20
tags:
---
#Separated Presentation

Ensure that any code that manipulates presentation only manipulates presentation, pushing all domain and data source logic into clearly separated areas of the program.

保证操作展现的代码仅仅在操作展现，将所有的领域和数据逻辑放到程序里一个更纯粹的地方。

##How it Works

This pattern is a form of layering, where we keep presentation code and domain code in separate layers with the domain code unaware of presentation code. This style came into vogue with Model-View-Controller architecture and is widely used.

这种模式是一种分层的形式，我们可以将展现逻辑代码和领域逻辑代码放到一个单独的层去。这样让领域代码毫不关心展现代码。这种方式随着MVC框架的流行被广泛应用。

To use it you begin by looking at all the data and behavior in a system and looking to see if that code is involved with the presentation. Presentation code would manipulate GUI widgets and structures in a rich client application, HTTP headers and HTML in a web application, or command line arguments and print statements in a command line application. We then divide the application into two logical modules with all the presentation code in one module and the rest in another module.

在使用这种模式之前，你应该通览下系统的整体数据和行为，然后看看这些代码是否掺杂着显示逻辑。在一个富客户端应用中展现代码通常会操作用户界面控件和框架，在一个web应用中则操作HTTP头和HTML，或者在命令行界面中打印参数和语句。我们把应用分为两个逻辑组成模块，一个模块是展现，其余的为另一个模块。

Further layering is often used to separate data source code from domain (business logic), and to separate the the domain using a Service Layer. For the purposes of Separated Presentation we can ignore these further layers, just referring to all of this as 'the domain layer'. Just bear in mind that further layering of the domain layer is likely.

分层通常用在从领域逻辑或者业务逻辑中分离出数据源代码，或者通过服务层来分离领域层。为了突出Separated Presentation的目的，我们暂时可以忽略这些层，仅仅只把他们统称为领域层。仅仅在心里先想着，将来也可以分层。

The layers are a logical and not a physical construct. Certainly you may find a physical separation into different tiers but this is not required (and a bad idea if not necessary). You also may see a separation into different physical packaging units (eg Java jars or .NET assemblies), but again this isn't required. Certainly it is good to use any logical packaging mechanisms (Java packages, .NET namespaces) to separate the layers.

这些层只是逻辑上而不是物理上的结构。你可能发现物理上不同的层也分离了，但是这些不是必须的。你也可以把分层体现在不同的物理打包单元里。类似于java的jar和.net的封装。但是再次强调，这个不是必须的。实现上比较好的事件时通过逻辑包名机制来分层。

As well as a separation, there is also a strict visibility rule. The presentation is able to call the domain but not vice-versa. This can be checked as part of a build with dependency checking tools. The point here is that the domain should be utterly unaware of what presentations may be used with it. This both helps keep the concers separate and also supports using multiple presentations with the same domain code.

同分离一样，这里也有一个严格的可见值。表现层通常可以调用领域层而不是相反。这个可以通过依赖检查工具来检查。在这里的点就是领域层必须毫不关心表现层。这样才有利于分离，并且也对多个界面使用同一个领域代码提供支持。

Although the domain cannot call the presentation it's often necessary for the domain to notify the presentation if any changes occur. Observer is the usual solution to this problem. The domain fires an event which is observed the by presentation, the presentation then re-reads data from the domain as needed.

即使领域层无法调用到显示层，通常而言当发生改变后，领域层需要通知到展现层。观察者模式通常是解决此类问题的方法。领域层会发送一个被展现层观察的事件，展现层会根据需要重新读取领域层数据。

A good mental test to use to check you are using Separated Presentation is to imagine a completely different user interface. If you are writing a GUI imagine writing a command line interface for the same application. Ask yourself if anything would be duplicated between the GUI and command line presentation code - if it is then it's a good candidate for moving to the domain.

一个好用的检查你是否使用Separated Presentation 技术的测试就是，想象一下你有一个完全不同的用户界面。如果你为该应用书写一个命令行界面，问问你自己 如果用户界面和命令行展现代码完全不变，它是否是一个移动到领域层的好的候选者。

##When to Use It

Example: Moving Domain Logic out of a Window (Java)

Most of examples you'll see from me follow Separated Presentation, simply because I find it such a fundamental design technique. Here's an example of how you can refactor a simple design that doesn't use Separated Presentation in order to use it.

你从我这看到的大多数例子基本都是Separated Presentation，因为我发现了一个基本的设计技术，所以非常简单。这里有个重构的例子，为了使不用Separated Presentation 的例子使用Separated Presentation 。

The example comes from the running example of Ice Cream Atmospheric Monitor. The main task I use to illustrate this is calculating the variance between target and actual and coloring the field to indicate the amount of this variance. You can imagine this done in the assessment window object like this:

这个例子来源于冰激凌监控器。最主要的任务就是展现计算target和actual之间的值，并且给variance上色来标示它。你可以想象代码是这么运转的。

class AssessmentWindow...
  private JFormattedTextField dateField, actualField, targetField,
  varianceField;
  Reading currentReading;

   private void updateVarianceField() {
       if (null == currentReading.getActual()) {
           varianceField.setValue(null);
           varianceField.setForeground(Color.BLACK);
       }
       else {
           long variance = currentReading.getActual() - currentReading.getTarget();
           varianceField.setValue(variance);
           long varianceRatio = Math.round(100.0 * variance / currentReading.getTarget());
            if (varianceRatio < -10) varianceField.setForeground(Color.RED);
            else if (varianceRatio > 5) varianceField.setForeground(Color.GREEN);
            else varianceField.setForeground(Color.BLACK);
        }
   }
As you can see this routine mixes the domain problem of calculating the variance with the behavior of updating the the variance text field. The Reading object, that holds the actual and target data, is here a data class - an anemic collection of fields and accessors. Since this is the object that has the data, it should be the one to calculate the variance.

你可以看到这段代码掺杂了领域的问题 （计算差值并且更新）。Reading对象持有actual 和 target数据，这里应该有个数据类，有个域和访问器的集合。因为对象含有数据，它应该来计算变量。

To start this I can use Replace Temp with Query on the variance calculation itself, to yield this.

我用Replace Temp with Query这个重构手法来重构variance计算本身。

class AssessmentWindow...
  private void updateVarianceField() {
       if (null == currentReading.getActual()) {
           varianceField.setValue(null);
           varianceField.setForeground(Color.BLACK);
       }
       else {
           varianceField.setValue(getVariance());
           long varianceRatio = Math.round(100.0 * getVariance() / currentReading.getTarget());
            if (varianceRatio < -10) varianceField.setForeground(Color.RED);
            else if (varianceRatio > 5) varianceField.setForeground(Color.GREEN);
            else varianceField.setForeground(Color.BLACK);
        }
   }
   private long getVariance() {
       return currentReading.getActual() - currentReading.getTarget();
   }
   
With the calculation now in its own method, I can safely move it to the Reading object.

重构到这里的话，它就变成自身的方法，就可以完全放心安全地挪到Reading对象中。

class AssessmentWindow...
   private void updateVarianceField() {
        if (null == currentReading.getActual()) {
            varianceField.setValue(null);
            varianceField.setForeground(Color.BLACK);
        }
        else {
            varianceField.setValue(currentReading.getVariance());
            long varianceRatio = Math.round(100.0 * currentReading.getVariance() / currentReading.getTarget());
             if (varianceRatio < -10) varianceField.setForeground(Color.RED);
             else if (varianceRatio > 5) varianceField.setForeground(Color.GREEN);
             else varianceField.setForeground(Color.BLACK);
         }
    }
class Reading...
    public long getVariance() {
        return getActual() - getTarget();
    }


I can do the same thing with the varianceRatio, I'll just show the final result but again I do it to steps (creating the local method then moving it) as I'm less likely to mess it up that way - particularly with the refactoring editor (IntelliJ Idea) I'm using.

如法炮制 我们针对varianceRatio 也来这套。

class AssessmentWindow...
   private void updateVarianceField() {
        if (null == currentReading.getActual()) {
            varianceField.setValue(null);
            varianceField.setForeground(Color.BLACK);
        }
        else {
            varianceField.setValue(currentReading.getVariance());
            if (currentReading.getVarianceRatio() < -10) varianceField.setForeground(Color.RED);
            else if (currentReading.getVarianceRatio() > 5) varianceField.setForeground(Color.GREEN);
            else varianceField.setForeground(Color.BLACK);
         }
    }

class Reading...
   public long getVarianceRatio() {
        return Math.round(100.0 * getVariance() / getTarget());
    }
    
The calculations are looking better, but I'm still not too happy. The logic about when the color should be one color or another is domain logic, even though the choice of color (and the fact that text color is the presentation mechanism) is presentation logic. What I need to do is split the out the determination of which category of variance we have (and the logic for assigning these categories) from the coloring.

计算逻辑看起来很好了。但是还不够，决定variance显示什么色的逻辑应该也是领域逻辑，即使颜色选择是展现逻辑。我所需要做的就是将决定variance是什么类别的逻辑从上色代码中分开。

There's no formalized refactoring here, but what I need is a method like this on Reading.

这里没有什么标准化的重构手法，下边是我需要的领域层的方法。

class Reading...
    public enum VarianceCategory {LOW, NORMAL, HIGH}

    public VarianceCategory getVarianceCategory() {
         if (getVarianceRatio() < -10) return VarianceCategory.LOW;
         else if (getVarianceRatio() > 5) return VarianceCategory.HIGH;
         else return VarianceCategory.NORMAL;
    }

class AssessmentWindow...
    private void updateVarianceField() {
        if (null == currentReading.getActual()) {
            varianceField.setValue(null);
            varianceField.setForeground(Color.BLACK);
        }
        else {
            varianceField.setValue(currentReading.getVariance());
            if (currentReading.getVarianceCategory() == Reading.VarianceCategory.LOW) varianceField.setForeground(Color.RED);
            else if (currentReading.getVarianceCategory() == Reading.VarianceCategory.HIGH) varianceField.setForeground(Color.GREEN);
            else varianceField.setForeground(Color.BLACK);
         }
    }

That's better, I now have the domain decisions in the domain object. But things are a bit messy, the presentation shouldn't have to know that the variance is null if the actual reading is null. That kind of dependency should be encapsulated in the Reading class.

这样会好一点。我让领域层对象去做领域层的决定。但有一点比较混乱，显示层不应该知道一些细节。这些都行都封装到Reading类中。

class Reading...
   public Long getVariance() {
        if (null == getActual()) return null;
        return getActual() - getTarget();
    }

class AssessmentWindow...
    private void updateVarianceField() {
        varianceField.setValue(currentReading.getVariance());
        if (null == currentReading.getVariance()) {
            varianceField.setForeground(Color.BLACK);
        }
        else {
            if (currentReading.getVarianceCategory() == Reading.VarianceCategory.LOW) varianceField.setForeground(Color.RED);
            else if (currentReading.getVarianceCategory() == Reading.VarianceCategory.HIGH) varianceField.setForeground(Color.GREEN);
            else varianceField.setForeground(Color.BLACK);
         }
    }

I can further encapsultate this by adding a null variance category, which also allows me to use a switch that I find easier to read.

进一步的，我添加了null这个类别。它将允许我用一个易读的switch结构。

class Reading...
  public enum VarianceCategory {
        LOW, NORMAL, HIGH, NULL}

    public VarianceCategory getVarianceCategory() {
        if (null == getVariance()) return VarianceCategory.NULL;
        if (getVarianceRatio() < -10) return VarianceCategory.LOW;
        else if (getVarianceRatio() > 5) return VarianceCategory.HIGH;
        else return VarianceCategory.NORMAL;
    }

class AssessmentWindow...
   private void updateVarianceField() {
        varianceField.setValue(currentReading.getVariance());
        switch (currentReading.getVarianceCategory()) {
            case LOW:
                varianceField.setForeground(Color.RED);
                break;
            case HIGH:
                varianceField.setForeground(Color.GREEN);
                break;
            case NULL:
                varianceField.setForeground(Color.BLACK);
                break;
            case NORMAL:
                varianceField.setForeground(Color.BLACK);
                break;
            default:
                throw new IllegalArgumentException("Unknown variance category");
        }
     }

As a last step, although not connected to Separated Presentation, I prefer to clean that switch up to remove the duplication.

最后一步与Separated Presentation无关，我倾向于调整下switch结构。

class AssessmentWindow...
  private void updateVarianceField() {
        varianceField.setValue(currentReading.getVariance());
        varianceField.setForeground(varianceColor());
    }

    private Color varianceColor() {
        switch (currentReading.getVarianceCategory()) {
            case LOW:
                return Color.RED;
            case HIGH:
                return Color.GREEN;
            case NULL:
                return Color.BLACK;
            case NORMAL:
                return Color.BLACK;
            default:
                throw new IllegalArgumentException("Unknown variance category");
        }
    }
    
This makes it obvious that what we have here is a simple table lookup. I could replace this by populating and indexing out of a hash. In some languages I might do that, but I find the switch here to be nice and readable in Java.

这段代码很明显得展示出 这里我们仅仅做了一个表查询。我可以把它封装成一个hash。在一些语言我可能这么做，但我发现java里的switch可能会更好。


