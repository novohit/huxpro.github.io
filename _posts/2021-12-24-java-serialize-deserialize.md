---
layout: post
title: Java对象序列化与反序列化
date: 2021-12-24
author: "Novo"
header-img: "img/post-bg-2023.jpg"
tags: [Java]
---



## 概述

Java 提供了一种对象**序列化**的机制。用一个字节序列可以表示一个对象，该字节序列包含该`对象的数据`、`对象的类型`和`对象中存储的属性`等信息。字节序列写出到文件之后，相当于文件中**持久保存**了一个对象的信息。

反之，该字节序列还可以从文件中读取回来，重构对象，对它进行**反序列化**。`对象的数据`、`对象的类型`和`对象中存储的数据`信息，都可以用来在内存中创建对象。

简单来说：

-   **序列化**： 将数据结构或对象转换成二进制字节流的过程
-   **反序列化**：将在序列化过程中所生成的二进制字节流转换成数据结构或者对象的过程

对于 Java
这种面向对象编程语言来说，我们序列化的都是对象（Object）也就是实例化后的类(Class)，但是在
C++这种半面向对象的语言中，struct(结构体)定义的是数据结构类型，而 class
对应的是对象类型。

维基百科：

> **序列化**（serialization）在计算机科学的数据处理中，是指将数据结构或对象状态转换成可取用格式（例如存成文件，存于缓冲，或经由网络中发送），以留待后续在相同或另一台计算机环境中，能恢复原先状态的过程。依照序列化格式重新获取字节的结果时，可以利用它来产生与原始对象相同语义的副本。对于许多对象，像是使用大量引用的复杂对象，这种序列化重建的过程并不容易。面向对象中的对象序列化，并不概括之前原始对象所关系的函数。这种过程也称为对象编组（marshalling）。从一系列字节提取数据结构的反向操作，是反序列化（也称为解编组、deserialization、unmarshalling）。

综上：**序列化的主要目的是通过网络传输对象或者说是将对象存储到文件系统、数据库、内存中。**

![](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/typora/2_zhuanhuan.jpg)

![img](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/typora/a478c74d-2c48-40ae-9374-87aacf05188c.png)

## ObjectOutputStream类

`java.io.ObjectOutputStream`
类，将Java对象的原始数据类型写出到文件,实现对象的持久存储。

### 构造方法

-   `public ObjectOutputStream(OutputStream out)`：
    创建一个指定OutputStream的ObjectOutputStream。

构造举例，代码如下：

``` java
FileOutputStream fileOut = new FileOutputStream("employee.txt");
ObjectOutputStream out = new ObjectOutputStream(fileOut);
```

### 序列化操作

1.  一个对象要想序列化，必须满足两个条件:

-   该类必须实现`java.io.Serializable` 接口，`Serializable`
    是一个标记接口，不实现此接口的类将不会使任何状态序列化或反序列化，会抛出`NotSerializableException`
    。

-   该类的所有属性必须是可序列化的。如果有一个属性**不需要可序列化**的，则该属性必须注明是**瞬态**的，使用`transient`
    关键字修饰。

-   被`static`修饰的属性也不会被序列化(因为`static`属性不属于对象)

    > **注意：**
    >
    > -   `transient` 只能修饰变量，不能修饰类和方法。
    >
    > -   `transient`
    >     修饰的变量，在反序列化后变量值将会被置成类型的默认值。例如，如果是修饰
    >     `int` 类型，那么反序列后结果就是 `0`。

``` java
public class Employee implements java.io.Serializable {
    public String name;
    public String address;
    public transient int age; // transient瞬态修饰成员,不会被序列化
    public void addressCheck() {
        System.out.println("Address  check : " + name + " -- " + address);
    }
}
```

2.写出对象方法

-   `public final void writeObject (Object obj)` : 将指定的对象写出。

``` java
public class SerializeDemo{
    public static void main(String [] args)   {
        Employee e = new Employee();
        e.name = "zhangsan";
        e.address = "beiqinglu";
        e.age = 20; 
        try {
            // 创建序列化流对象
          ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("employee.txt"));
            // 写出对象
            out.writeObject(e);
            // 释放资源
            out.close();
            fileOut.close();
            System.out.println("Serialized data is saved"); // 姓名，地址被序列化，年龄没有被序列化。
        } catch(IOException i)   {
            i.printStackTrace();
        }
    }
}
输出结果：
Serialized data is saved
```

## ObjectInputStream类

`ObjectInputStream`反序列化流，将之前使用`ObjectOutputStream`序列化的原始数据恢复为对象。

### 构造方法

-   `public ObjectInputStream(InputStream in)`：
    创建一个指定InputStream的ObjectInputStream。

### 反序列化操作1

如果能找到一个对象的class文件，我们可以进行反序列化操作，调用`ObjectInputStream`读取对象的方法：

-   `public final Object readObject ()` : 读取一个对象。

``` java
public class DeserializeDemo {
   public static void main(String [] args)   {
        Employee e = null;
        try {       
             // 创建反序列化流
             FileInputStream fileIn = new FileInputStream("employee.txt");
             ObjectInputStream in = new ObjectInputStream(fileIn);
             // 读取一个对象
             e = (Employee) in.readObject();
             // 释放资源
             in.close();
             fileIn.close();
        }catch(IOException i) {
             // 捕获其他异常
             i.printStackTrace();
             return;
        }catch(ClassNotFoundException c)  {
            // 捕获类找不到异常
             System.out.println("Employee class not found");
             c.printStackTrace();
             return;
        }
        // 无异常,直接打印输出
        System.out.println("Name: " + e.name);  // zhangsan
        System.out.println("Address: " + e.address); // beiqinglu
        System.out.println("age: " + e.age); // 0
    }
}
```

**对于JVM可以反序列化对象，它必须是能够找到class文件的类。如果找不到该类的class文件，则抛出一个
`ClassNotFoundException` 异常。**

### 反序列化操作2

**另外，当JVM反序列化对象时，能找到class文件，但是class文件在序列化对象之后发生了修改，那么反序列化操作也会失败，抛出一个`InvalidClassException`异常。**发生这个异常的原因如下：

-   该类的序列版本号与从流中读取的类描述符的版本号不匹配
-   该类包含未知数据类型
-   该类没有可访问的无参数构造方法

`Serializable`
接口给需要序列化的类，提供了一个序列版本号。`serialVersionUID`
该版本号的目的在于验证序列化的对象和对应类是否版本匹配。

``` java
public class Employee implements java.io.Serializable {
     // 加入序列版本号
     private static final long serialVersionUID = 1L;
     public String name;
     public String address;
     // 添加新的属性 ,重新编译, 可以反序列化,该属性赋为默认值.
     public int eid; 

     public void addressCheck() {
         System.out.println("Address  check : " + name + " -- " + address);
     }
}
```

## 案例：序列化集合

1.  将存有多个自定义对象的集合序列化操作，保存到`list.txt`文件中。
2.  反序列化`list.txt` ，并遍历集合，打印对象信息。

### 案例分析

1.  把若干学生对象 ，保存到集合中。
2.  把集合序列化。
3.  反序列化读取时，只需要读取一次，转换为集合类型。
4.  遍历集合，可以打印所有的学生信息

### 案例实现

``` java
public class SerTest {
    public static void main(String[] args) throws Exception {
        // 创建 学生对象
        Student student = new Student("test1", "address1");
        Student student2 = new Student("test2", "address2");
        Student student3 = new Student("test3", "address3");

        ArrayList<Student> arrayList = new ArrayList<>();
        arrayList.add(student);
        arrayList.add(student2);
        arrayList.add(student3);
        // 序列化操作
        // serializ(arrayList);
        
        // 反序列化  
        ObjectInputStream ois  = new ObjectInputStream(new FileInputStream("list.txt"));
        // 读取对象,强转为ArrayList类型
        ArrayList<Student> list  = (ArrayList<Student>)ois.readObject();
        
        for (int i = 0; i < list.size(); i++ ){
            Student s = list.get(i);
            System.out.println(s.getName()+"--"+ s.getPwd());
        }
    }

    private static void serializ(ArrayList<Student> arrayList) throws Exception {
        // 创建 序列化流 
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("list.txt"));
        // 写出对象
        oos.writeObject(arrayList);
        // 释放资源
        oos.close();
    }
}
```
