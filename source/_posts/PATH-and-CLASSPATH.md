title: PATH and CLASSPATH
date: 2016-03-11 02:16:16
tags:
---
#PATH and CLASSPATH

This section explains how to use the PATH and CLASSPATH environment variables on Microsoft Windows, Solaris, and Linux.

这篇文章主要描述在各个操作系统中如何使用PATH和CLASSPATH环境变量。

 Consult the installation instructions included with your installation of the Java Development Kit (JDK) software bundle for current information.
 
 查询下你的JDK软件的安装包里边的安装指导，可以获取当前信息。
 
After installing the software, the JDK directory will have the structure shown below.

在安装完软件后，JDK的目录会如以下结构所示。

JDK directory structure

![image](https://docs.oracle.com/javase/tutorial/figures/essential/environment-directories.gif)



The bin directory contains both the compiler and the launcher.

bin文件夹中会包含编译器和启动器。

##Update the PATH Environment Variable (Microsoft Windows)

忽略

##Update the PATH Variable (Solaris and Linux)
You can run the JDK just fine without setting the PATH variable, or you can optionally set it as a convenience. However, you should set the path variable if you want to be able to run the executables (javac, java, javadoc, and so on) from any directory without having to type the full path of the command. 

你可以在不设置PATH变量的基础上完美运行JDK。或者你也可以把它当作一个可选项。然而你应该设置环境变量，如果你打算直接从任何目录中执行命令（java javac javadoc 或者其他）而不需要键入命令的完全长路径。

If you do not set the PATH variable, you need to specify the full path to the executable every time you run it, such as:

% /usr/local/jdk1.7.0/bin/javac MyClass.java

如果你不设置PATH的话，这意味着你每次运行他们的时候，都必须输入完全路径。

To find out if the path is properly set, execute:
% java -version
This will print the version of the java tool, if it can find it. If the version is old or you get the error java: Command not found, then the path is not properly set.

那么如何检查你的环境变量设置正确了呢？请执行java -version命令。它将会在环境变量路径正确的情况下，打印java工具的版本。如果版本太老或者你得到 “Command not found”的错误提示，就说明你的环境变量没有正确设置。

To set the path permanently, set the path in your startup file.
为了永久地设置环境变量，需要在不同版本的shell中的启动文件中设置路径。

For C shell (csh), edit the startup file (~/.cshrc):
set path=(/usr/local/jdk1.7.0/bin $path)

For bash, edit the startup file (~/.bashrc):
PATH=/usr/local/jdk1.7.0/bin:$PATH
export PATH

For ksh, the startup file is named by the environment variable, ENV. To set the path:
PATH=/usr/local/jdk1.7.0/bin:$PATH
export PATH

For sh, edit the profile file (~/.profile):
PATH=/usr/local/jdk1.7.0/bin:$PATH
export PATH

对应csh，bash，ksh，sh 对应上述四种设置环境变量的方式。

Then load the startup file and verify that the path is set by repeating the java command:

For C shell (csh):
% source ~/.cshrc
% java -version

For ksh, bash, or sh:
% . /.profile
% java -version

对于不同平台，需要加载下刚刚设置环境变量的启动文件，并重复执行命令以验证路径是否设置正确。

##Checking the CLASSPATH variable (All platforms)

The CLASSPATH variable is one way to tell applications, including the JDK tools, where to look for user classes. (Classes that are part of the JRE, JDK platform, and extensions should be defined through other means, such as the bootstrap class path or the extensions directory.)

CLASSPATH变量是一种告诉所有应用 （包括JDK工具）去哪里查找用户类的方式。 （JRE JDK的类和扩展类应该通过其他方式 譬如bootstrap classpath 或者 extensions来查找）

The preferred way to specify the class path is by using the -cp command line switch. This allows the CLASSPATH to be set individually for each application without affecting other applications. Setting the CLASSPATH can be tricky and should be performed with care.

更好的方式，是通过命令行里的-cp 来制定类路径。它允许CLASSPATH可以在不影响其他应用的前提下单独为每个应用设置。设置 CLASSPATH应该有些小技巧并小心执行。


The default value of the class path is ".", meaning that only the current directory is searched. Specifying either the CLASSPATH variable or the -cp command line switch overrides this value.

默认的类路径是 "." 意味着只有当前路径被搜索。制定其他的CLASSPATH路径或者使用-cp 选项会覆盖此值。

To check whether CLASSPATH is set on Microsoft Windows NT/2000/XP, execute the following:
C:> echo %CLASSPATH%

On Solaris or Linux, execute the following:
% echo $CLASSPATH

If CLASSPATH is not set you will get a CLASSPATH: Undefined variable error (Solaris or Linux) or simply %CLASSPATH% (Microsoft Windows NT/2000/XP).

To modify the CLASSPATH, use the same procedure you used for the PATH variable.

在两个平台上，可以通过上述方式来查看CLASSPATH是否设置成功。
如果想修改CLASSPATH的值，可以查看设置PATH的步骤，照猫画虎。

Class path wildcards allow you to include an entire directory of .jar files in the class path without explicitly naming them individually. 

类路径的通配符可以允许你包含整整一个目录里边的jar文件，而不用手动一个一个指出。

For more information, including an explanation of class path wildcards, and a detailed description on how to clean up the CLASSPATH environment variable, see the Setting the Class Path technical note.

包含类路径通配符的描述，如何清理CLASSPATH环境变量的详细信息的 更多信息，可以查看https://docs.oracle.com/javase/8/docs/technotes/tools/windows/classpath.html

