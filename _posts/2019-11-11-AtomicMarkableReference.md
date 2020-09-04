---
layout:     post
title:      AtomicMarkableReference
subtitle:   AtomicMarkableReference
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
public class AtomicMarkableReference<V>
```
AtomicMarkableReference维护一个对象引用和一个标记位，标记位可以自动更新。

注意:此实现通过创建表示“已装箱”[reference, boolean]对的内部对象来维护可标记的引用。

### 1.5




# Constructor
```java
    private static class Pair<T> {
        final T reference;
        final boolean mark;
        private Pair(T reference, boolean mark) {
            this.reference = reference;
            this.mark = mark;
        }
        static <T> Pair<T> of(T reference, boolean mark) {
            return new Pair<T>(reference, mark);
        }
    }

    private volatile Pair<V> pair;

    /**
     * 使用给定的初始值创建一个新的AtomicMarkableReference。
     * 
     * @param initialRef the initial reference
     * @param initialMark the initial mark
     */
    public AtomicMarkableReference(V initialRef, boolean initialMark) {
        pair = Pair.of(initialRef, initialMark);
    }

```









# Method
## getReference
```java
    public V getReference() {
        return pair.reference;
    }
```
返回引用的当前值。








## isMarked
```java
    public boolean isMarked() {
        return pair.mark;
    }
```
返回标记的当前值。








## get
```java
    public V get(boolean[] markHolder) {
        Pair<V> pair = this.pair;
        markHolder[0] = pair.mark;
        return pair.reference;
    }

```
返回引用和标记的当前值。
### Param
markHolder - 至少一个大小的数组。返回时，markholder[0]将保存标记的值。
### Return
引用的当前值










## weakCompareAndSet
```java
    public boolean weakCompareAndSet(V       expectedReference,
                                     V       newReference,
                                     boolean expectedMark,
                                     boolean newMark) {
        return compareAndSet(expectedReference, newReference,
                             expectedMark, newMark);
    }

```
原子地将引用和标记的值设置为给定的更新值(如果当前引用==预期引用且当前标记等于预期标记)。

可能会错误地失败，并且不提供排序保证，所以很少是compareAndSet的合适替代。
### Param
expectedReference - 引用期望的值
newReference - 引用的新值
expectedMark - 标记的期望的值
newMark - 标记的新值
### Return
如果成功返回true










## compareAndSet
```java
    public boolean compareAndSet(V       expectedReference,
                                 V       newReference,
                                 boolean expectedMark,
                                 boolean newMark) {
        Pair<V> current = pair;
        return
            expectedReference == current.reference &&
            expectedMark == current.mark &&
            ((newReference == current.reference &&
              newMark == current.mark) ||
             casPair(current, Pair.of(newReference, newMark)));
    }

```
原子地将引用和标记的值设置为给定的更新值(如果当前引用==预期引用且当前标记等于预期标记)。













## set
```java
    public void set(V newReference, boolean newMark) {
        Pair<V> current = pair;
        if (newReference != current.reference || newMark != current.mark)
            this.pair = Pair.of(newReference, newMark);
    }

```
无条件地设置引用和标记的值。








## attemptMark
```java
    public boolean attemptMark(V expectedReference, boolean newMark) {
        Pair<V> current = pair;
        return
            expectedReference == current.reference &&
            (newMark == current.mark ||
             casPair(current, Pair.of(expectedReference, newMark)));
    }

```
原子地将标记的值设置为给定的更新值(如果当前引用==指定的引用)。这个操作的任何给定调用都可能会错误地失败(返回false)，但是当当前值持有期望值并且没有其他线程也试图设置该值时，重复调用将最终成功。












## Unsafe mechanics
```java

// 获取unsafe
    private static final sun.misc.Unsafe UNSAFE = sun.misc.Unsafe.getUnsafe();
    // 获得属性值偏移量
    private static final long pairOffset =
        objectFieldOffset(UNSAFE, "pair", AtomicMarkableReference.class);

// cas操作
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