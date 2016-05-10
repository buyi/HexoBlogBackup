title: Annotation(part 1)
date: 2015-12-31 01:06:21
tags:
---
#Annotations

Annotations, a form of metadata, provide data about a program that is not part of the program itself. Annotations have no direct effect on the operation of the code they annotate.

注解，一种元数据的格式。提供与程序相关但不是程序本身的一些数据。Annotations对于他们声明的代码的操作并无直接影响。

Annotations have a number of uses, among them:

- Information for the compiler — Annotations can be used by the compiler to detect errors or suppress warnings.
- Compile-time and deployment-time processing — Software tools can process annotation information to generate code, XML files, and so forth.
- Runtime processing — Some annotations are available to be examined at runtime.

注解共有以下几种用处，他们分别是：
1.为编译器提供信息。
注解可以被编译器所用，来探测错误或者抑制警告
2.编译时 部属时解析
可作为一种软件工具，可以通过解析注解信息来生成代码，xml文件或者其他
3.运行时解析
一些注解可以在运行时解析

This lesson explains where annotations can be used, how to apply annotations, what predefined annotation types are available in the Java Platform, Standard Edition (Java SE API), how type annnotations can be used in conjunction with pluggable type systems to write code with stronger type checking, and how to implement repeating annotations.

本教程主要讲述注解都会在哪些地方被使用，如何应用注解，有哪些java官方平台预定义的注解，类型注解可以跟插入类型系统一起去写强类型检查的代码，如何应用重复注解。
