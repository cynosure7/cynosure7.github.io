---
date: 2008-09-27 19:17:00+00:00
layout: post
title: (Learn Java) How Java Code Works
category: tech
tags: Java
---
#Background
This post is a very basic introduction to Java, it briefly share the mechanism of Java work flow. In fact, it is worth digging around in every component and process in this work flow. But here is just a summary which will not include any details.
#Hello World
## Java source code
Java source code is code that you write in Java programming language. The file extension is .java.

For example, Hello.java:

```java
public class Hello {
	public static void main(String[] args)
	{
		System.out.println("Hello World!");
	}
}
```

##Java Compiler
Java compiler is a program that translates Java source code into the Java bytecodes which is executed by Java Virtual Machine (JVM). `javac hello.java`

![indexed](http://HelloOwen.com/album/java/compiler.jpg)  

##Java Bytecode
When Java source file is compiled by Java compiler it is converted into Java class file with .class extension. .class file of each class is separately stored. Its name is same as class name in source file. The class file contains Java bytecode (highly optimized instruction set stored into bytes). A .class file can be executed on any operating system; we just need JVM of that operating system. This makes Java machine independent. We can disassembles Hello.class (javap -c Hello) and see the instructions:

```java
class Hello extends java.lang.Object{
Hello();
  Code:
   0:   aload_0
   1:   invokespecial   #1; //Method java/lang/Object."<init>":()V
   4:   return

public static void main(java.lang.String[]);
  Code:
   0:   getstatic       #2; //Field java/lang/System.out:Ljava/io/PrintStream;
   3:   ldc     #3; //String Hello World!
   5:   invokevirtual   #4; //Method java/io/PrintStream.println:(Ljava/lang/Str
ing;)V
   8:   return

}
```

##Java Virtual Machine

JVM is a specification that provides runtime environment in which java bytecode can be executed. There are various implementations provided by Sun and other companies. It is an abstraction layer between a Java application and the underlying platform. The JVM takes your ".class" file, loads it into its virtual memory, links lots of stuff together, and then starts interpreting/executing the program. During linking, your class file will be combined with other classes that are part of the Java environment. Standard classes exist for helping you do things like displaying text on the display, get characters typed on the keyboard, display graphics stuff, communicate over the Internet,...  And, then, off it goes; your program comes to life.

In fact, there are so many things to say about JVM. Here I'll just briefly introduce the structure of JVM, and the function of the main components.
![indexed](http://HelloOwen.com/album/java/JVM_Arc.png)

###Class Loader
ClassLoader is responsible for loading class files from file system, network or any other source.

###Runtime Data Area
During execution of a program, JVM defines various run-time data areas.

There are 6 JVM data areas divided into 2 categories:

1. Thread shared data areas: Method Area, Heap and Direct Memory - These data areas are created at JVM start-up time and shared by all threads.

2. Per-thread data areas: PC Register, JVM Stack, Native Method Stack - These data areas are created when a thread is created and used by that thread only. If multiple threads are running, multiple copies of these data areas will be created.

###Execution Engine
Execution Engine converts the byte code into the machine code so that the processor will execute it and display results.

It has two parts:
1. Interpreter
2. Just-in-time compiler

Interpreter is a program which interpret bytecode to native machine language at run time. For it uses additional processor and memory during interpretation, Java application performs more slowly than a native application. So just-in-time compiler comes here to improve the performance of Java programs by compiling bytecodes into native machine code at run time. It's usually faster to work with JIT compiler, especially if the method executable is repeatedly reused. 

#Reference
http://www.bfoit.org/Intro_to_Programming/JavaProgram.html

http://www.javalaunch.com/Compiler%20Vs%20Interpreter.html

http://www.cs.cmu.edu/~jcarroll/15-100-s05/supps/basics/history.html

http://www.thecrazyprogrammer.com/2014/06/what-is-class-file-and-bytecode-in-java.html

https://www.youtube.com/watch?v=t8sQw3pGJzM

http://www.javalaunch.com/WhatisJVM.html

http://www.herongyang.com/JVM/Data-Area-What-Are-Runtime-Data-Areas.html

[Understanding the IBM Software Developers Kit (SDK) for Java - JIT compiler overview](http://www-01.ibm.com/support/knowledgecenter/SSYKE2_7.0.0/com.ibm.java.aix.70.doc/diag/understanding/jit_overview.html)

http://whatis.techtarget.com/definition/just-in-time-compiler-JIT

http://www.javatpoint.com/internal-details-of-jvm