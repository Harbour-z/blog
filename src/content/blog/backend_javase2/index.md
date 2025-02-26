---
title: 后端学习 — JavaSE下
author: Harbour-z
description: '后端学习Java基础 - 笔记 1，主要是语法基础的查缺补漏，下一篇开始面向对象。'
tags:
  - Backend
publishDate: 2025-02-19
draft: false
language: '中文'
---

## 面向对象高级-1

这部分涉及：static关键字、设计模式：单例、继承

> static解决数据被所有对象共享的问题；单例是让类对外只能产生一个对象的解决方案；实体类代码复用的问题由继承来解决

### Static 关键字

#### 修饰成员变量

静态，可修饰成员变量、成员方法。

成员变量按照有无static修饰可分为两种：

* 类变量：属于类，只在计算机中存储一份，会被类的全部对象共享
* 实例变量（对象的变量）：属于每个对象

类变量的访问：

```java
//推荐
ClassName.Variable
//也可以但不推荐
对象.类变量
```



课程视频中讲解了一个应用场景还挺有意思的：系统启动后要求用户类能够自动计数创建了多少个用户对象。

```java
public class User{
    public static int number;
    
    public User(){
        User.number++;
    }
}
```

之前没有想过能直接修改构造器来实现方法，很有意思。

#### 修饰成员方法

同样类方法和实例方法

类方法推荐：`类名.类方法`来访问

类方法最常见的应用场景是做工具类，给开发人员共同使用的；工具类中方法都是类方法，每个方法完成一个功能；这样做提高了代码复用，调用方便，提高了开发效率。

如果用实例方法，需要创建对象会占用内存。

**工具类没有创建对象的需求，建议将工具类的构造器进行私有**，也即用private来修饰构造器。

**注意事项**：

1. 类方法中可以直接访问类的成员，不可以直接访问实例成员
2. 实例方法中既可以直接访问类成员，也可以访问实例成员
3. 实例方法中可以出现`this`关键字，类方法中不可以出现关键字

#### 代码块

代码块是类的五大成分之一：成员变量、构造器、方法、代码块、内部类。

**定义**：代码块是用`{}`括起来的一段代码。在Java类中，根据位置和声明方式不同，分为普通代码块，构造代码块，静态代码块和同步代码块。

* **普通代码块**：定义在方法中的代码块，主要用于限制变量的作用域。例如：

```java
public class CodeBlockExample {
    public void testMethod() {
        {
            int localVar = 10;
            System.out.println("普通代码块中的变量: " + localVar);
        }
        // 这里不能访问localVar，因为它超出了作用域
    }
}
```

- **构造代码块**：定义在类中但在方法体外的代码块，每次创建对象时都会执行，优先于构造方法执行，用于初始化一些通用的对象属性。主要用于提取多个构造方法中的共同代码部分，实现代码复用

  例如：

```java
public class Person {
    String name;
    int age;

    {
        age = 18; // 给所有Person对象的age初始值设为18
    }

    public Person(String n) {
        name = n;
    }
}
```

- **静态代码块**：使用`static`修饰的代码块，在类加载时执行，且只执行一次，常用于初始化类变量（完成类的初始化）等操作。例如：

  > 这里如果是简单逻辑如赋值等就可以直接来赋值，但是执行复杂逻辑就需要静态代码块来执行；比如定义了一个类变量，就不能在类中静态代码块之外的地方为它赋值

```java
public class StaticCodeBlockExample {
    static int staticVar;

    static {
        staticVar = 100;
        System.out.println("静态代码块执行，初始化staticVar为 " + staticVar);
    }
}
```

- **同步代码块**：主要用于多线程编程中，通过`synchronized`关键字修饰，确保在同一时刻只有一个线程能够执行该代码块，用于解决多线程访问共享资源时的线程安全问题。例如：

```java
public class SynchronizedCodeBlockExample {
    private static final Object lock = new Object();

    public void synchronizedMethod() {
        synchronized (lock) {
            // 线程安全的代码
        }
    }
}
```

#### 单例设计模式

设计模式：具体问题的最优解法。

单例设计模式：确保一个类只能有一个对象

**写法**：

1. 把类的构造器私有

2. 定义一个类变量，记住一个类的对象

3. 定义一个类方法，返回对象

```java
public class A{
    private static A a = new A();
    private A(){
        
    }
    
    public static A getObject(){
        return a; 
    }
}
```



单例设计模式实现方式很多：比如饿汉式单例、懒汉式单例

### 继承

关键字`extends`使两个类之间建立起父子关系

```java
public class B extends A{
    
}
```

A为父类/基类/超类；B子类/派生类

子类能继承父类的非私有成员（成员变量、成员方法）；子类的对象由子类和父类共同完成

继承可以提高代码复用性

![权限修饰符](./quanxian.png)

这里protected的任意包的子类的含义是：在其他包的继承了父类的类中可以访问



Java只支持单继承，不支持多继承，但是可以多层继承。多继承指的是一个类直接从多个父类继承属性和行为的能力

> 指的是比如类B继承A，C继承B，D继承C是可以的

方法重写，子类可以重写一个与父类方法名称，参数列表都一样的方法，去覆盖父类的方法；子类复写父类方法时，访问权限必须大于或者等于父类方法的权限。

一个不继承父类的类会默认继承Object类；所有类都是Object的子孙类



