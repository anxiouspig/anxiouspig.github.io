---
layout:     post
title:      AtomicStampedReference
subtitle:   AtomicStampedReference
date:       2019-11-11
author:     anxious pig
header-img: img/pexels-photo-1936936.jpeg
catalog: true
tags:
    - java8
    - JDK源码
    - 并发
---


# Summary
```java
public class AtomicStampedReference<V>
```
AtomicStampedReference维护一个对象引用和一个可以自动更新的整数“stamp”。

实现注意:此实现通过创建表示“已装箱”[reference, integer]对的内部对象来维护标记的引用。
### Since 1.5






# Constructor
```java

    private static class Pair<T> {
        final T reference;
        final int stamp;
        private Pair(T reference, int stamp) {
            this.reference = reference;
            this.stamp = stamp;
        }
        static <T> Pair<T> of(T reference, int stamp) {
            return new Pair<T>(reference, stamp);
        }
    }

    private volatile Pair<V> pair;


    public AtomicStampedReference(V initialRef, int initialStamp) {
        pair = Pair.of(initialRef, initialStamp);
    }
```
使用给定的初始值创建一个新的AtomicStampedReference。









# Method
## getReference
```java
    public V getReference() {
        return pair.reference;
    }

```
返回引用的当前值。






## getStamp
```java
    public int getStamp() {
        return pair.stamp;
    }

```
返回戳记的当前值。









## get
```java
    public V get(int[] stampHolder) {
        Pair<V> pair = this.pair;
        stampHolder[0] = pair.stamp;
        return pair.reference;
    }

```
返回引用和戳记的当前值。








## weakCompareAndSet
```java
    public boolean weakCompareAndSet(V   expectedReference,
                                     V   newReference,
                                     int expectedStamp,
                                     int newStamp) {
        return compareAndSet(expectedReference, newReference,
                             expectedStamp, newStamp);
    }

```
如果当前引用==预期引用，且当前戳记等于预期戳记，则自动将引用和戳记的值设置为给定的更新值。

可能会错误地失败，并且不提供排序保证，所以很少是compareAndSet的合适替代。









## compareAndSet
```java
    public boolean compareAndSet(V   expectedReference,
                                 V   newReference,
                                 int expectedStamp,
                                 int newStamp) {
        Pair<V> current = pair;
        return
            expectedReference == current.reference &&
            expectedStamp == current.stamp &&
            ((newReference == current.reference &&
              newStamp == current.stamp) ||
             casPair(current, Pair.of(newReference, newStamp)));
    }

```
如果当前引用==预期引用，且当前戳记等于预期戳记，则自动将引用和戳记的值设置为给定的更新值。









## set
```java
    public void set(V newReference, int newStamp) {
        Pair<V> current = pair;
        if (newReference != current.reference || newStamp != current.stamp)
            this.pair = Pair.of(newReference, newStamp);
    }

```
无条件地设置引用和戳记的值。








## attemptStamp
```java
    public boolean attemptStamp(V expectedReference, int newStamp) {
        Pair<V> current = pair;
        return
            expectedReference == current.reference &&
            (newStamp == current.stamp ||
             casPair(current, Pair.of(expectedReference, newStamp)));
    }

```
如果当前引用是==，则自动将戳记的值设置为给定的更新值。这个操作的任何给定调用都可能会错误地失败(返回false)，但是当当前值持有期望值并且没有其他线程也试图设置该值时，重复调用将最终成功。







## Unsafe mechanics
```java
    private static final sun.misc.Unsafe UNSAFE = sun.misc.Unsafe.getUnsafe();
    private static final long pairOffset =
        objectFieldOffset(UNSAFE, "pair", AtomicStampedReference.class);

    private boolean casPair(Pair<V> cmp, Pair<V> val) {
        return UNSAFE.compareAndSwapObject(this, pairOffset, cmp, val);
    }

    static long objectFieldOffset(sun.misc.Unsafe UNSAFE,
                                  String field, Class<?> klazz) {
        try {
            return UNSAFE.objectFieldOffset(klazz.getDeclaredField(field));
        } catch (NoSuchFieldException e) {
            // Convert Exception to corresponding Error
            NoSuchFieldError error = new NoSuchFieldError(field);
            error.initCause(e);
            throw error;
        }
    }

```