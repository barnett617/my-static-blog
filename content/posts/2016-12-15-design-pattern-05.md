---
title: 可复用面向对象软件基础——设计模式（五）之建造者模式
tags: ["设计模式"]
date: 2016-12-15 20:22:46
lastmod: 2016-12-15 20:22:46
series: ["设计模式"]
---

工厂类模式提供的是创建单个类的模式，而建造者模式则是将各种产品**集中**起来进行**管理**，用来创建**复合对象**，所谓复合对象就是指某个类具有不同的属性，其实建造者模式就是前面抽象工厂模式和最后的 Test 结合起来得到的。

<!-- more -->

## 代码实现

```java
/**
 * 发送接口，有一个发送方法待实现
 * @author barnett
 *
 */
public interface Sender {

  public void send();

}
```

```java
/**
 * 2、邮件发送类，实现发送接口，实现其发送方法，用以发送邮件
 * @author barnett
 *
 */
public class MailSender implements Sender {

  @Override
  public void send() {
    System.out.println("I am MailSender!");
  }

}
```

```java
/**
 * 短信发送类，实现了发送接口的发送方法，用以发送短信
 * @author barnett
 *
 */
public class SmsSender implements Sender {

  @Override
  public void send() {
    System.out.println("I am SmsSender!");
  }

}
```

```java
/**
 * 3、建造者类
 * @author barnett
 *
 */
public class Builder {

  // 用以存储生产出的多个发送器
  private List<Sender> list = new ArrayList<Sender>();

  /**
   * 用于生产邮件发送器，当该方法被调用时会生产多个邮件发送器放入集合
   * @param count	生产个数
   */
  public void produceMailSender(int count) {
    for(int i=0; i<count; i++) {
      list.add(new MailSender());
    }
    for(Sender sender : list) {
      sender.send();
    }
  }

  public void produceSmsSender(int count)	{
    for(int i=0; i<count; i++) {
      list.add(new SmsSender());
    }
  }

}

```

```java
/**
 * 4、测试类
 * @author barnett
 *
 */
public class Test {

  public static void main(String[] args) {
    // 实例化一个建造者
    Builder builder = new Builder();
    // 传入参数，委派建造者生产多个产品并放入集合
    builder.produceMailSender(5);
  }

}
```

## 与工厂模式对比

> 从这点看出，建造者模式将很多功能**集成**到一个类里，这个类可以创造出比较复杂的东西。所以与工程模式的区别就是：工厂模式关注的是创建**单个产品**，而建造者模式则关注创建符合对象的**多个**部分。因此，是选择工厂模式还是建造者模式，依实际情况而定。
