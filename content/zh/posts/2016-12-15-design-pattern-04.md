---
title: 可复用面向对象软件基础——设计模式（四）之单例模式
tags:
  - 设计模式
date: 2016-12-15 19:57:13
update: 2016-12-15 19:57:13
series: ['设计模式']
categories: 笔记
---

### 一、单例模式（Singleton）简介

> 单例对象（Singleton）是一种常用的设计模式。在Java应用中，单例对象能保证在一个JVM中，该对象**只有一个实例**存在。

### 二、单例模式优势

 1. 某些类创建繁琐，对于一些大型对象，系统开销大
 2. 省去new操作符，降低系统内存使用频率，减轻GC压力
 3. 保证某些核心类独立控制系统整个流程（控制其不可实例多个）

### 三、简版单例类

```java
public class Singleton {

	// 持有私有静态实例，防止被引用，此处赋值为null，目的为实现延迟加载
	private static Singleton instance = null;
	
	// 私有构造方法，防止被实例化
	private Singleton() {

	}
	
	// 静态工厂方法
	public static Singleton getInstance() {
		if(instance == null) {
			instance = new Singleton();
		}
		return instance;
	}
	
	// 如果该对象被用于序列化，可保证对象在序列化前后保持一致
	public Object readResolve() {
		return instance;
	}
}
```

> 这个类可以满足基本要求，但是，像这样毫无**线程安全**保护的类，如果我们把它放入**多线程**的环境下，肯定就会出现问题了，如何解决？我们首先会想到对getInstance方法加**synchronized**关键字，如下：
> 

```java
	// 静态工厂方法
	public static synchronized Singleton getInstance() {
		if(instance == null) {
			instance = new Singleton();
		}
		return instance;
	}
```

> 但是，synchronized关键字锁住的是这个**对象**，这样的用法，在**性能**上会有所**下降**，因为每次调用getInstance()，都要对对象上锁。
> 

```java
	// 静态工厂方法
	public static Singleton getInstance() {
		if(instance == null) {
			synchronized (instance) {
				if(instance == null) {
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
```

> 似乎解决了之前提到的问题，将synchronized关键字加在了内部，也就是说当调用的时候是不需要加锁的，只有在instance为null，并**创建对象**的时候才需要加锁，性能有一定的提升。
> 
> 但是，这样的情况，还是有可能有问题的，看下面的情况：在Java指令中**创建对象**和**赋值**操作是分开进行的，也就是说instance = new Singleton();语句是分两步执行的。
> 
> 但是JVM并不保证这两个操作的**先后顺序**，也就是说有可能JVM会为新的Singleton实例分配空间，然后**直接赋值给instance成员**，然后再去**初始化这个Singleton实例**。这样就可能出错了。
> 
> 我们以A、B两个线程为例：
1>A、B线程同时进入了第一个if判断
2>A首先进入synchronized块，由于instance为null，所以它执行instance = new Singleton();
3>由于JVM内部的优化机制，JVM先画出了一些分配给Singleton实例的**空白内存**，并赋值给**instance成员**（注意此时JVM没有开始初始化这个实例），然后A离开了synchronized块。
4>B进入synchronized块，由于instance此时不是null，因此它马上离开了synchronized块并将结果返回给调用该方法的程序。
5>此时B线程打算使用Singleton实例，却发现它**没有被初始化**，于是错误发生了。
> 
> 所以程序还是有可能发生错误，其实程序在运行过程是很复杂的，从这点我们就可以看出，尤其是在写**多线程**环境下的程序更有难度，有挑战性。我们对该程序做进一步优化：

### 四、内部类维护单例
> 实际情况是，单例模式使用**内部类**来维护单例的实现，JVM内部的机制能够保证当一个类被加载的时候，这个类的加载过程是**线程互斥**的。
> 
> 这样当我们第一次调用getInstance的时候，JVM能够帮我们保证instance只被创建一次，并且会保证把赋值给instance的内存**初始化完毕**，这样我们就不用担心上面的问题。
> 
> 同时该方法也只会在第一次调用的时候使用互斥机制，这样就解决了低性能问题。
> 
> 其实说它完美，也不一定，如果在**构造函数**中抛出**异常**，实例将永远得不到创建，也会出错。
> 
> 所以说，十分完美的东西是没有的，我们只能根据实际情况，选择最适合自己应用场景的实现方法。
```java
public class Singleton {
	
	// 私有构造方法，防止被实例化
	private Singleton() {
		
	}
	
	// 使用内部类维护单例
	private static class SingletonFactory {
		private static Singleton instance = new Singleton();
	}
	
	// 获取实例
	public static Singleton getInstance() {
		return SingletonFactory.instance;
	}
	
	// 若该对象被用于序列化，可保证对象在序列化前后保持一致
	public Object readResolve() {
		return getInstance();
	}
}
```
### 五、单独为创建加Synchronized

> 
> 也有人这样实现：因为我们只需要在创建类的时候进行同步，所以只要将创建和getInstance()分开，单独为创建加synchronized关键字，也是可以的：

```java
public class SingletonTest {
	
	private static SingletonTest instance = null;
	
	private SingletonTest() {
		
	}
	
	private static synchronized void syncInit() {
		if(instance == null) {
			instance = new SingletonTest();
		}
	}
	
	public static SingletonTest getInstance() {
		if(instance == null) {
			syncInit();
		}
		return instance;
	}

}
```