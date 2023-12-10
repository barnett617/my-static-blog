---
title: 可复用面向对象软件基础——设计模式（六）之原型模式
tags: ["设计模式"]
date: 2016-12-15 21:06:11
lastmod: 2016-12-15 21:06:11
series: ["设计模式"]
---

原型模式虽然是创建型的模式，但是与工程模式没有关系，从名字即可看出，该模式的思想就是将一个对象作为原型，对其进行**复制、克隆**，产生一个和原对象类似的新对象。

<!-- more -->

## 一、原型模式（Prototype）

```java
/**
 * 原型类，实现可复制接口
 * @author barnett
 *
 */
public class Prototype implements Cloneable {

  /**
   * 复制方法，调用父类的复制方法
   */
  public Object clone() throws CloneNotSupportedException {
    Prototype prototype = (Prototype) super.clone();
    return prototype;
  }
}
```

> 很简单，一个原型类，只需要实现**Cloneable**接口，覆写 clone 方法，此处 clone 方法可以改成任意的名称，因为 Cloneable 接口是个**空接口**，你可以任意定义实现类的方法名，如 cloneA 或者 cloneB。
>
> 因为此处的重点是 super.clone()这句话，super.clone()调用的是 Object 的 clone()方法，而在 Object 类中，clone()是 native 的。

## 二、深浅复制

> **浅复制**：将一个对象复制后，基本数据类型的变量都会重新创建，而**引用类型**，**指向**的还是**原对象所指向的**。
>
> **深复制**：将一个对象复制后，不论是基本数据类型还有引用类型，都是**重新创建**的。简单来说，就是深复制进行了**完全彻底**的复制，而浅复制不彻底。

```java
public class PrototypeDeepAndThinClone implements Cloneable, Serializable {

  private static final long serialVersionUID = 1L;
  private String string;

  private SerializableObject obj;

  // 浅复制
  public Object clone() throws CloneNotSupportedException {
    Prototype prototype = (Prototype) super.clone();
    return prototype;
  }

  // 深复制
  public Object deepClone() throws IOException, ClassNotFoundException {

    // 写入当前对象的二进制流
    ByteArrayOutputStream bos = new ByteArrayOutputStream();
    ObjectOutputStream oos = new ObjectOutputStream(bos);
    oos.writeObject(this);

    // 读出二进制流产生的新对象
    ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
    ObjectInputStream ois = new ObjectInputStream(bis);
    return ois.readObject();
  }

  public String getString() {
    return string;
  }

  public void setString(String string) {
    this.string = string;
  }

  public SerializableObject getObj() {
    return obj;
  }

  public void setObj(SerializableObject obj) {
    this.obj = obj;
  }

}

class SerializableObject implements Serializable {
  private static final long serialVersionUID = 1L;
}
```

> 要实现深复制，需要采用**流**的形式读入当前对象的**二进制输入**，再写出**二进制数据**对应的对象。
