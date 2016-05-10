title: Annotation(part 2)
date: 2016-03-14 15:49:02
tags:
---
Annotations Basics

The Format of an Annotation

In its simplest form, an annotation looks like the following:

@Entity

在最简单的情况下，一个注解长@Entity这样。

The at sign character (@) indicates to the compiler that what follows is an annotation. In the following example, the annotation's name is Override:

@Override
void mySuperMethod() { ... }

@这个符号来告知编译器，后边跟着的是一个注解。
在下边这个例子里，注解的名字是Override

The annotation can include elements, which can be named or unnamed, and there are values for those elements:

注解可以包括元素，这些元素可以命名也可以不命名，并且还有对应的元素值


@Author(
  name = "Benjamin Franklin",
  date = "3/27/2003"
)
class MyClass() { ... }





or


@SuppressWarnings(value = "unchecked")
void myMethod() { ... }





If there is just one element named value, then the name can be omitted, as in:

@SuppressWarnings("unchecked")
void myMethod() { ... }

如果只有一个元素的话，可以省略其名称。
If the annotation has no elements, then the parentheses can be omitted, as shown in the previous @Override example.

如果一个注解一个元素都没有的话，则括号都可以省略了，就像上文中的Override一样。
It is also possible to use multiple annotations on the same declaration:
对于一个声明，我们可以使用多个注解。


@Author(name = "Jane Doe")
@EBook
class MyClass { ... }





If the annotations have the same type, then this is called a repeating annotation:

如果注解是相同的类型，则他们就成为重复注解。


@Author(name = "Jane Doe")
@Author(name = "John Smith")
class MyClass { ... }





Repeating annotations are supported as of the Java SE 8 release. For more information, see Repeating Annotations.

重复注解在Java8中被支持，具体信息可以查看下文。

The annotation type can be one of the types that are defined in the java.lang or java.lang.annotation packages of the Java SE API. In the previous examples, Overrideand SuppressWarnings are predefined Java annotations. It is also possible to define your own annotation type. The Author and Ebook annotations in the previous example are custom annotation types.

注解类型是一种定义在java.lang.annotation或者java.lang j2se API的类型之一。在前边的例子里，Override 和 SuppressWarning是预定义Java注解。我们也可以定义我们自己的注解类型，之前的Author和Ebook注解就是自定义注解类型。

Where Annotations Can Be Used

Annotations can be applied to declarations: declarations of classes, fields, methods, and other program elements. When used on a declaration, each annotation often appears, by convention, on its own line.

注解可以被应用在声明上。
类的声明 class XXX
域的声明
方法的声明
以及其他程序要素
按照惯例，当使用一个声明时，每一个注解都会和声明出现在同一行。
As of the Java SE 8 release, annotations can also be applied to the use of types. Here are some examples:

随着java8的发布，注解也可以用在类型上

Class instance creation expression:

    new @Interned MyObject();
类实例创建语法

Type cast:

    myString = (@NonNull String) str;
类型切换

implements clause:

    class UnmodifiableList<T> implements
        @Readonly List<@Readonly T> { ... }
接口实现语法

Thrown exception declaration:

    void monitorTemperature() throws
        @Critical TemperatureException { ... }
异常抛出声明

This form of annotation is called a type annotation. For more information, see Type Annotations and Pluggable Type Systems.

这种格式的注解称为类型注解，更多信息可以查看Type Annotations and Pluggable Type Systems.
