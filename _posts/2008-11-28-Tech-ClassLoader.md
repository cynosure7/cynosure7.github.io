---
date: 2008-11-28 10:29:00+00:00
layout: post
title: (Learn Java) Introduction to ClassLoader
category: tech
tags: ClassLoader Java
---
#What is ClassLoader
Java ClassLoader is used to load classes at runtime. We all know that Java code is compiled into bytecode (.class file) by Java compiler and then JVM executes bytecode. It is ClassLoader's responsibility to load class files from file system, network or any other resources.

On the whole, ClassLoader loads, verifies, prepare and initializes a class from a java class file.

1. Load the bytecode from Java class file.
2. Verify if the class file is well-formed and check the class file contents to ensure that the code does not attempt to do wrong.
3. Prepare the storage space for static class fields, creates methods tables which speed up virtual method calls, and object templates, which speed up object creation.
4. Loads all the other classes referenced by a particular class.
5. Initialize class's initialization method, (if defined) initialize static class fields to user-defined initial values (if specified).

#How ClassLoader Works
Java provides a dynamic load feature; it loads a class when it is referred for the first time at runtime (for example: YourClass a = new YourClass(); YourClass.doSomthing();), not compile time.

For example: we have HelloWorld.java and Printer.java in folder `C:\Users\ocao\Desktop\Hello`:

```java
public class HelloWorld {
	public static void main(String[] args)
	{
		Printer.greet();
	}
}
```

```java
public class Printer {
	public static final String GREETING = "Hello World!";
	public static void greet() {
		System.out.println(GREETING);
	}
}
```
Now we compile and run it:

`javac -classpath C:\Users\ocao\Desktop\Hello HelloWorld.java`

`java -verbose:class -classpath C:\Users\ocao\Desktop\Hello HelloWorld`

_[-verbose:class ](http://java.dzone.com/articles/how-use-verbose-options-java)is used to display the information about classes being loaded by JVM._

Here is a part of output:

>
[Opened C:\Program Files\Java\jre7\lib\rt.jar]  
[Loaded java.lang.Object from C:\Program Files\Java\jre7\lib\rt.jar]  
[Loaded java.io.Serializable from C:\Program Files\Java\jre7\lib\rt.jar]  
...  
[Loaded java.security.BasicPermissionCollection from C:\Program Files\Java\jre7\lib\rt.jar]  
[Loaded HelloWorld from file:/C:/Users/ocao/Desktop/Hello/]  
[Loaded java.lang.Void from C:\Program Files\Java\jre7\lib\rt.jar]  
[Loaded Printer from file:/C:/Users/ocao/Desktop/Hello/]  
Hello World!  
[Loaded java.lang.Shutdown from C:\Program Files\Java\jre7\lib\rt.jar]  
[Loaded java.lang.Shutdown$Lock from C:\Program Files\Java\jre7\lib\rt.jar]  

We can see that Class Printer is loaded for it's (static) referred by Class HelloWorld.

In fact, although it's a very simple application, we can see that there are quite a number of classes loaded, and most of them are loaded from rt.jar. `rt` stands for Runtime, this jar contains all the classes provided in the Java Runtime Environment. And these classes in rt.jar are loaded by Bootstrap ClassLoader. Bootstrap is system class loader and it is parent of all class loaders in Java. Here comes the key of classLoader, the **parent-delegation model**.

##The Parent-Delegation Model
The delegation model requires that any request for a class loader to load a given class is first delegated to its parent class loader before the requested class loader tries to load the class itself. The parent class loader, in turn, goes through the same process of asking its parent. This chain of delegation continues through to the bootstrap class loader. If a class loader's parent can load a given class, it returns that class. Otherwise, the class loader attempts to load the class itself.

The JVM has three class loaders, each possessing a different scope from which it can load classes. As you descend the hierarchy, the scope of available class repositories widens, and typically the repositories are less trusted:

`
Bootstrap
|
Extensions
|
Application
`
At the top of the hierarchy is the bootstrap class loader. This class loader is responsible for loading only the classes that are from the core Java™ API (JRE/lib/rt.jar).

The extensions class loader can load classes that are standard extensions packages in the extensions directory (JRE/lib/ext or any directory denoted by java.ext.dirs).

The application class loader can load classes from the local file system, and will load files from the CLASSPATH (CLASSPATH environment variable, -classpath or -cp option, Class-Path attribute of Manifest inside JAR file). The application class loader is the parent of any custom class loader or hierarchy of custom class loaders.

This parent-delegation model is introduced for these 2 factors: 

1. Avoid repeating load
Instead of loading the class directly, a classloader will ask its parent firstly. It will not load the class if its parent has done, so it can avoid repeating class load.

2. Security
Because class loading is always delegated first to the parent of the class loading hierarchy, the most trusted repository (the core API) is checked first, followed by the standard extensions, then the local files that are on the class path. Finally, classes that are located in any repository that your own class loader can access, are accessible. This system prevents code from less-trusted sources from replacing trusted core API classes by assuming the same name as part of the core API.

##Namespaces and the Runtime Package

Loaded classes are identified by both the class name and the class loader that loaded it. This separates loaded classes into namespaces that the class loader identifies.

Namespaces cause classes to be segregated by class loader, thereby preventing less-trusted code loaded from the application or custom class loaders from interacting directly with more trusted classes. You can grant special access privileges between classes that are in the same package by the use of package or protected access. This gives access rights between classes of the same package, but only if they were loaded by the same class loader. This stops code from an untrusted source trying to insert a class into a trusted package. As discussed earlier, the delegation model prevents the possibility of replacing a trusted class with a class of the same name from an untrusted source. The use of namespaces prevents the possibility of using the special access privileges that are given to classes of the same package to insert code into a trusted package.

#ClassLodaer Practice
##Custom Class Loaders
You might want to write your own class loader so that you can load classes from an alternate repository, partition user code, or unload classes.

There are three main reasons why you might want to write your own class loader.

1. To allow class loading from alternative repositories.  
   This is the most common case, in which an application developer might want to load classes from other locations, for example, over a network connection.
2. To partition user code.  
   This case is less frequently used by application developers, but widely used in servlet engines.
3. To allow the unloading of classes.  
   This case is useful if the application creates large numbers of classes that are used for only a finite period. Because a class loader maintains a cache of the classes that it has loaded, these classes cannot be unloaded until the class loader itself has been dereferenced. For this reason, system and extension classes are never unloaded, but application classes can be unloaded when their class loader is.


#Reference

[Understanding the IBM Software Developers Kit (SDK) for Java>Class loading](http://www-01.ibm.com/support/knowledgecenter/SSYKE2_7.0.0/com.ibm.java.aix.70.doc/diag/understanding/class_loader.html?lang=en)

http://javarevisited.blogspot.com/2012/12/how-classloader-works-in-java.html

http://javarevisited.blogspot.sg/2012/07/when-class-loading-initialization-java-example.html

http://javapapers.com/core-java/java-class-loader/

http://tutorials.jenkov.com/java-reflection/dynamic-class-loading-reloading.html

http://www.programcreek.com/2013/01/when-and-how-a-java-class-is-loaded-and-initialized/

http://www.iteye.com/topic/83978

https://www.youtube.com/watch?v=t8sQw3pGJzM

[Understanding the class loader - How to write a custom class loader](http://publib.boulder.ibm.com/infocenter/javasdk/v1r4m2/index.jsp?topic=%2Fcom.ibm.java.doc.diagnostics.142%2Fhtml%2Fid1100.html)