---
layout:     post
title:      LinkedHashSet
subtitle:   LinkedHashSet
date:       2019-11-11
author:     anxious pig
header-img: img/pexels-photo-1936936.jpeg
catalog: true
tags:
    - java8
    - JDK源码
    - 数据结构与算法
---

# Summary
```java
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
```

```java
private static final long serialVersionUID = -2851667679971038690L;
```
哈希表和链表实现了Set接口，具有可预测的迭代顺序。这个实现与HashSet的不同之处在于，它维护一个运行于所有条目中的双链表。这个链表定义了迭代顺序，即元素插入集合的顺序(插入顺序)。注意，如果一个元素被重新插入到集合中，插入顺序不会受到影响。

这个实现使它的客户端免受HashSet提供的未指定的、通常混乱的排序的影响，而不会增加与TreeSet相关的成本。它可以用来生成与原始集合顺序相同的集合的副本，而不管原始集合的实现是什么:
```java

     void foo(Set s) {
         Set copy = new LinkedHashSet(s);
         ...
     }
```
该类提供所有可选的Set操作，并允许空元素。与HashSet一样，它为基本操作(添加、包含和删除)提供了固定时间的性能，假设hash函数正确地将元素分散到各个桶中。由于维护链表的额外开销，性能可能略低于HashSet，但有一个例外:LinkedHashSet上的迭代需要与集合大小成比例的时间，而与它的容量无关。哈希集的迭代可能更昂贵，需要的时间与它的容量成正比。

链接哈希集有两个影响其性能的参数:初始容量和负载因子。它们被精确地定义为HashSet。但是，请注意，为初始容量选择过高值的代价对于该类来说没有HashSet那么严重，因为该类的迭代时间不受容量的影响。

如果多个线程同时访问一个链接的散列集，并且至少有一个线程修改该散列集，则必须在外部同步该散列集。这通常是通过对一些自然封装了集合的对象进行同步来实现的。如果不存在这样的对象，则应该使用集合来“包装”集合。synchronizedSet方法。这最好在创建时完成，以防止意外的不同步访问集:
```java
   Set s = Collections.synchronizedSet(new LinkedHashSet(...));
```
这个类的iterator方法返回的迭代器是快速故障的:如果在创建迭代器之后随时修改集合，除了通过迭代器自己的remove方法，迭代器将抛出ConcurrentModificationException。因此，在面对并发修改时，迭代器会快速而干净地失败，而不是在将来某个不确定的时间冒着任意的、不确定的行为的风险。

注意，不能保证迭代器的快速故障行为，因为通常来说，在存在非同步并发修改的情况下，不可能做出任何严格的保证。故障快速迭代器以最大的努力抛出ConcurrentModificationException。因此，编写一个依赖于此异常来判断其正确性的程序是错误的:迭代器的快速故障行为应该只用于检测bug。

该类是Java集合框架的成员。
### Type Param
E - 此集合维护的元素的类型
### Since 1.4






# Constructor
```java
    public LinkedHashSet(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor, true);
    }
```
使用指定的初始容量和负载因子构造一个新的空链接哈希集。




```java
    public LinkedHashSet(int initialCapacity) {
        super(initialCapacity, .75f, true);
    }
```
构造一个新的空链接哈希集，该哈希集具有指定的初始容量和缺省加载因子(0.75)。





```java
    public LinkedHashSet() {
        super(16, .75f, true);
    }
```
使用默认初始容量(16)和负载因子(0.75)构造一个新的空链接哈希集。





```java
    public LinkedHashSet(Collection<? extends E> c) {
        super(Math.max(2*c.size(), 11), .75f, true);
        addAll(c);
    }
```
使用与指定集合相同的元素构造新的链接哈希集。创建链接的散列集时，初始容量足以容纳指定集合中的元素和缺省负载因子(0.75)。



# Method
## spliterator
```java
    @Override
    public Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, Spliterator.DISTINCT | Spliterator.ORDERED);
    }
```
在此集合中的元素上创建延迟绑定和故障快速Spliterator。Spliterator报告Spliterator。大小,Spliterator。不同的命令。实现应该记录额外特征值的报告。