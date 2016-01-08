---
layout: post
title: 函数式接口和Lambda表达式 
description: 函数式接口和Lambda表达式 
category: blog
---
# 函数式接口和Lambda表达式 #

相信每个Java开发者对下面的接口都不会陌生：`java.lang.Runnable, java.awt.event.ActionListener, java.util.Comparator, java.util.concurrent.Callable. `，这些接口有个特点就是接口内只声明了一个方法，所以这样的接口也被称为SAM接口（**S**ingle **A**bstract **M**ethod interfaces）。使用这种接口普遍的方式是通过匿名内部类，比如：

    public class AnonymousInnerClassTest {
      public static void main(String[] args) {
        new Thread(new Runnable() {
          @Override
          public void run() {
            System.out.println("A thread created and running ...");
          }
        }).start();
      }
    }

Java8也提出了一个同SAM接口相似的概念，叫做函数式接口（Functional interfaces）。这样就可以使用Lambda表达式、方法引用（Method reference）和构造引用（constructor references）。Java8还推出了`@FunctionalInterface`注解用于在编译期间检查该接口是否是函数式接口（类似于@Override）。下面是只有一个方法的函数式接口

    @FunctionalInterface
    public interface SimpleFuncInterface {
      public void doWork();
    }

与SAM接口不同的是，除了可以声明一个抽象方法，函数式接口还可以声名多个来自于'java.lang.Object'类的方法，比如：

    @FunctionalInterface
    public interface SimpleFuncInterface {
      public void doWork();
      public String toString();
      public boolean equals(Object o);
    }

但是如果要声明两个除了Object类里方法的抽象方法的话，IDE就会报错。
![](http://7xprht.com1.z0.glb.clouddn.com/lambdaQQ%E6%88%AA%E5%9B%BE20160108105759.png)

需要注意的是，如果一个函数试接口继承自另一个函数式接口，那么这个接口就不能再声明新的抽象方法了。
![](http://7xprht.com1.z0.glb.clouddn.com/lambdaQQ%E6%88%AA%E5%9B%BE20160108110838.png)

但是被default修饰符修饰的方法却可以在被继承的函数式接口中声明

    @FunctionalInterface
    public interface ComplexFunctionalInterface extends SimpleFuncInterface {
      default public void doSomeWork(){
        System.out.println("Doing some work in interface impl...");
      }
      default public void doSomeOtherWork(){
        System.out.println("Doing some other work in interface impl...");
      }
    }

为什么要提出函数式接口？其实这是为Lambda表达式服务的。只有符合函数式接口才能使用Lambda表达式。
Lambda表达式由参数列表、表达式、语句块三个部分组成，如
`(s) -> { s.length();}`
其中s是参数，->为表达式的语法，{}中的是语句块，这也可以简写成`s -> s.length()`。
下面我们对比一下使用匿名内部类和使用Lambda表达式两种方式去实现函数式接口：

    /*
    * Implementing the interface by creating an
    * anonymous inner class versus using 
    * lambda expression.
    */
    public class SimpleFunInterfaceTest {
      public static void main(String[] args) {
        carryOutWork(new SimpleFuncInterface() {
          @Override
          public void doWork() {
            System.out.println("Do work in SimpleFun impl...");
          }
        });
        carryOutWork(() -> System.out.println("Do work in lambda exp impl..."));
      }
      public static void carryOutWork(SimpleFuncInterface sfi){
        sfi.doWork();
      }
    }
输出：
> Do work in SimpleFun impl...
> Do work in lambda exp impl...

在使用IDE的时候，可以用Lambda表达式代替的时候IDE会给我们提示
![](http://7xprht.com1.z0.glb.clouddn.com/lambdaQQ%E6%88%AA%E5%9B%BE20160108111827.png)