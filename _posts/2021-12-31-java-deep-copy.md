---
layout: post
title: "如何理解引用拷贝、深拷贝和浅拷贝"
date: 2021-12-31
author: "Novo"
header-img: "img/post-bg-2023.jpg"
tags: [Java]
---



- 引用拷贝：引用拷贝，就是拷贝引用地址。两个不同的引用指向同一个对象。
- 浅拷贝：浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点）
  - 如果属性是基本类型(int,double,long,boolean等)，拷贝的就是基本类型的值
  - 如果属性是引用类型，拷贝的就是内存地址（即复制引用但不复制引用的对象）
  > 注：String类型通过常量赋值时相当于基本数据类型，由于**String为不可变对象**，是无法修改原String的状态的，其会生成一个新的String对象
  >
- 深拷贝 ：深拷贝会完全复制整个对象，包括这个对象所包含的内部对象。

图示：

![](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/typora/image-20211231192614327.png)

![](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/typora/image-20211231192742183.png)

![](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/typora/image-20211231192914746.png)



案例：

**实现浅拷贝**

1. 在需要拷贝的类上实现 `Cloneable` 接口，并重写 `clone()` 方法。实现很简单，直接调用的是父类 `Object` 的 `clone()` 方法。
2. 使用的时候调用实现类的`clone`方法

```java
public class Address implements Cloneable{
    private final String name;
    // 省略构造函数、Getter&Setter方法
    @Override
    public Address clone() {
        try {
            return (Address) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}

public class Person implements Cloneable {
    private Address address;
    // 省略构造函数、Getter&Setter方法
    @Override
    public Person clone() {
        try {
            Person person = (Person) super.clone();
            return person;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}
```

测试 ：

```java
Person person1 = new Person(new Address("武汉"));
Person person1Copy = person1.clone();
// true
System.out.println(person1.getAddress() == person1Copy.getAddress());
```

从输出结构就可以看出， `person1` 的克隆对象和 `person1` 使用的仍然是同一个 `Address` 对象。

如果改变该类的引用属性，克隆对象的引用属性也会随着改变。



**实现深拷贝**

1. 对 `Person` 类的 `clone()` 方法进行修改，连带着要把 `Person` 对象内部的 `Address` 对象一起复制。

```java
@Override
public Person clone() {
    try {
        Person person = (Person) super.clone();
        person.setAddress(person.getAddress().clone());
        return person;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
}
```

测试 ：

```java
Person person1 = new Person(new Address("武汉"));
Person person1Copy = person1.clone();
// false
System.out.println(person1.getAddress() == person1Copy.getAddress());
```

从输出结构就可以看出，虽然 `person1` 的克隆对象和 `person1` 包含的 `Address` 对象已经是不同的了。意味着深拷贝创建一个新的对象，是完全独立于原对象的，所以无论怎么修改属性，另一个对象中的属性也不会随着改变。



**实现引用拷贝** 

```java
 Person person = new Person();
 Person person1 = person;
```

无论是改变person还是person1的任何属性，都会跟着改变。
