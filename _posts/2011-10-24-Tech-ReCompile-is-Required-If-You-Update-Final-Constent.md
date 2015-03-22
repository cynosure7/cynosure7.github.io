---
date: 2011-10-24 16:24:00+00:00
layout: post
title: Recompile Is Required If You Update Compile-time Constant
category: tech
tags: Java Compile-time Constant
---
#Background
I changed a final constant, but it didn't work. I had replaced both the class A which contains constant and the class B uses the constant. But it seems Class B is also using the old value. I thought I may missed something.

Exactly, once the final constant is updated, I need to recompile the class which uses this constant.

#Why?
##A Simple Example
In short, the Java compiler replaces final constant values like `VALUE` with their literal values. For Example:

We define a static final constant `VALUE` in A.java

```java
public class A {
    public static final int VALUE = 200;
}
```

and use it in B.java

```java
public class B {
    public static void main(String[] args) {
        System.out.println(A.VALUE);
    }
}
```

Compile A.java and B.java. You'll get A.class and B.class. By decompiling with [JD-GUI](http://jd.benow.ca/), we can see class B:

```java
import java.io.PrintStream;

public class B
{
  public static void main(String[] args)
  {
    System.out.println(200);
  }
}
```

Or we can use [javap](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/javap.html) command to disassembles B.class (javap -c B), and the output is like this:

```java
Compiled from "B.java"
public class B extends java.lang.Object{
public B();
  Code:
   0:   aload_0
   1:   invokespecial   #8; //Method java/lang/Object."<init>":()V
   4:   return

public static void main(java.lang.String[]);
  Code:
   0:   getstatic       #16; //Field java/lang/System.out:Ljava/io/PrintStream;
   3:   sipush  200
   6:   invokevirtual   #22; //Method java/io/PrintStream.println:(I)V
   9:   return

}
```
The constant `VALUE` has been replaced by the value 200 (In disassembled code, the value of VALUE is hard-coded to 200 `3:   sipush  200`). So, if you change `VAULE` in class A later but you don't recompile class B, you will still see the old value in class B.

##Compile-time Constant
An important concept here is that the final field is initialized with a **compile time constant**. Obviously, a compile time constant is a constant value that is known at compile time. Here are general rules regarding compile time constants:

1. They must be declared final
2. They are of primitive data types or String
3. They must be initialized with their declaration.
4. Their value must be constant expression.

The first 3 rules are straightforward, about the last one, here is the definition of constant expression from JLS:
>15.28 Constant Expression  
A compile-time constant expression is an expression denoting a value of primitive type or a String that is composed using only the following:  
Literals of primitive type and literals of type String  
Casts to primitive types and casts to type String  
The unary operators +, -, ~, and ! (but not ++ or --)  
The multiplicative operators *, /, and %  
The additive operators + and -  
The shift operators <<, >>, and >>>  
The relational operators <, <=, >, and >= (but not instanceof)  
The equality operators == and !=  
The bitwise and logical operators &, ^, and |  
The conditional-and operator && and the conditional-or operator ||  
The ternary conditional operator ? :  
Simple names that refer to final variables whose initializers are constant expressions
Qualified names of the form TypeName . Identifier that refer to final variables whose initializers are constant expressions

And as you can see, it contains more than just literals. In fact, literals are merely the first bullet point on the list. Let's have a look at more valid values for compile time constants:

```java
final String topic = "Compile time constant" + " add"; //additive operators +
final boolean result = 3 > 2; //relational operators >
final int result = 3 * 2; //multiplicative operator *
```

All in all, for any compile time constant, compiler must be able to deduce their value.


#Reference
http://www.androidmix.com/compile-time-constants-in-java

http://docs.oracle.com/javase/specs/jls/se7/jls7.pdf

http://www.coderanch.com/t/454384/java/java/compile-time-constant