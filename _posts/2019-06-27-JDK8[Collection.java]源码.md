---
layout:     post
title:      JDK8[Collection.java]源码解析
subtitle:   JDK8[Collection.java]源码解析
date:       2019-06-27
author:     渣子哥
header-img: img/pexels-photo-1936936.jpeg
catalog: true
tags:
    - JDK8源码
    - java8
---

# 源码

```java
package java.util;

import java.util.function.Predicate;
import java.util.stream.Stream;
import java.util.stream.StreamSupport;

// 包含重复元素的无序集合直接实现此接口
// @see     Set
// @see     List
// @see     Map
// @see     SortedSet
// @see     SortedMap
// @see     HashSet
// @see     TreeSet
// @see     ArrayList
// @see     LinkedList
// @see     Vector
// @see     Collections
// @see     Arrays
// @see     AbstractCollection
// @since 1.2

public interface Collection<E> extends Iterable<E> {


    // @return 集合元素数量，最大Integer.MAX_VALUE
    int size();

    // @return 集合空为true
    boolean isEmpty();

    // @param 测试元素
    // @return 如果包含至少一个元素，返回true
    // @throws 可选，参数与集合元素类型不同，抛出 ClassCastException
    boolean contains(Object o);

    // 对集合返回的顺序没有保证，除非集合可以保证顺序
    // @return 返回集合中基于此元素的迭代器
    Iterator<E> iterator();

    // 如果集合保证了元素的顺序，则此方法必须返回相同顺序
    // 这个方法起到数组和集合桥梁的作用。
	// 集合必须是安全的的，返回的数组是重新分配的数组
    // @return 集合中包含所有元素的数组
    Object[] toArray();

    // 返回集合包含所有元素的数组
    // 返回数组运行时类型
    // 返回的数组不包含空的元素，因为有时数组元素会比集合元素多
    // 如果集合能保证顺序，则返回数组必须保证顺序
    // 这个方法起到数组和集合桥梁的作用。
    // String[] y = x.toArray(new String[0]);
    // @param <T> 包含集合的数组运行时类型
    // @return 包含所有集合元素的数组
    // @throws ArrayStoreException 如果数组的类型不是集合每个元素的超类型，则抛出此异常
    <T> T[] toArray(T[] a);

    // 可选：确保集合包括特定元素
    // 如果此集合因调用而更改，则返回true
    // 如果此集合不允许重复元素且已包含该元素，则返回false
    // 支持此操作的集合可能会限制向该集合添加哪些元素
    // 集合类应该在其文档中明确指定可以添加哪些元素的任何限制。
    // @param 确保集合中可以存在的元素
    // @return 如果加进去了，则返回true
    // @throws UnsupportedOperationException 如果添加操作不被集合支持
    // @throws ClassCastException 类型转换异常
    // @throws NullPointerException 空指针异常
    // @throws IllegalArgumentException 非法参数异常
    // @throws IllegalStateException 如果由于插入限制，此时不能添加元素
    boolean add(E e);

    // 移除集合中特定元素的实例 如果包含多个元素，大多情况下，移除一个。
    // 如果集合包含这个元素并且移除了，则返回true.
    // @param o 如果存在，从集合中移除的元素
    // @return <tt>true</tt> 如果元素被移除
    // @throws ClassCastException 如果元素类型与集合不相容（可选）
    // @throws NullPointerException 如果特定元素为null，而集合不允许null值（可选）
    // @throws UnsupportedOperationException 如果集合不支持移除
    boolean remove(Object o);

    // @param  c 检查是否包含在集合中的元素
    // @return <tt>true</tt> 如果集合包含所有特定集合的元素
    // @throws ClassCastException 如果特定集合中的一个或多个元素类型与集合不相符（类型）
    // @throws NullPointerException 集合不允许null（可选）
    // @see    #contains(Object)
    boolean containsAll(Collection<?> c);

    // @param c 添加到这个集合中包含元素的集合
    // @return <tt>true</tt> 如果加进去了
    // @throws UnsupportedOperationException 不支持添加所有的操作
    // @throws ClassCastException 如果特定集合类型不能加入这个集合
    // @throws NullPointerException 如果不允许添加null，或者这个集合就是null
    // @throws IllegalArgumentException 如果特定集合元素性质不能加入这个集合
    // @throws IllegalStateException 如果由于插入限制，此时不能添加元素
    // @see #add(Object)
    boolean addAll(Collection<? extends E> c);

    // 移除包含特定集合的所有元素 (可选).  After this call returns,
    // @param c 移除特定集合包含的元素
    // @return <tt>true</tt> 如果集合发生改变
    // @throws UnsupportedOperationException 如果removeAll方法不被支持
    // @throws ClassCastException 如果集合一个或多个元素与特定集合不相容（可选）
    // @throws NullPointerException 如果集合包含一个或多个空元素，而集合不支持（可选）
    //         或者集合为null
    // @see #remove(Object)
    // @see #contains(Object)
    boolean removeAll(Collection<?> c);

    // 按照指定谓词移除所有元素，迭代期间或谓词抛出的错误或运行时异常将传递给调用者。
    // @implSpec
    // 默认实现使用 {@link #iterator}. 每次匹配元素用{@link Iterator#remove()}移除.  如果结婚迭	        代不支持迭代，抛出 {@code UnsupportedOperationException} 异常在第一次运行时
    // @param 过滤的谓语
    // @return {@code true} 如果元素被移除
    // @throws NullPointerException 如果过滤为null
    // @throws UnsupportedOperationException 如果元素不能从集合移除
    // @since 1.8
	// TODO 以后补充逻辑
    default boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter);
        boolean removed = false;
        final Iterator<E> each = iterator();
        while (each.hasNext()) {
            if (filter.test(each.next())) {
                each.remove();
                removed = true;
            }
        }
        return removed;
    }

    // 仅保留此集合中包含在指定集合中的元素(可选操作)。换句话说，从这个集合中删除指定集合中不包含的所有元		素。
    // @param c 集合中要被保留的包含元素的集合
    // @return <tt>true</tt> 如果集合改变了
    // @throws UnsupportedOperationException 如果不支持retainAll
    // @throws ClassCastException 元素类型不相容（可选）
    // @throws NullPointerException 如果为null（可选）
    // @see #remove(Object)
    // @see #contains(Object)
    boolean retainAll(Collection<?> c);

    // 移除集合中所有元素 (可选).
    // @throws UnsupportedOperationException 如果不支持clear
    void clear();

    // 比较集合中特定对象相等，如有需要，请重写
    // @param o 跟集合比较相等的对象
    // @return <tt>true</tt> 如果特定对象与集合相等
    // @see Object#equals(Object)
    // @see Set#equals(Object)
    // @see List#equals(Object)
    boolean equals(Object o);

    // 返回集合hash值，重写equals()，必须也重写hashCode()
    // @return 集合hash值
    // @see Object#hashCode()
    // @see Object#equals(Object)
    int hashCode();

    // 创建一个 {@link Spliterator} 基于集合元素
    // 子类应该覆盖默认实现， 返回更高效的 spliterator.  
    // 主要为并行迭代，把集合分段，每个线程执行一个
    // {@link Spliterator#SUBSIZED}.
    // @return a {@code Spliterator} 基于集合元素
    // @since 1.8
    // TODO 以后研究
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }

    // 返回这个集合作为资源的Spliterator流
    // 这个方法应该被重写 当 {@link #spliterator()} 方法不能返回 spliterator
    // @return {@code Stream} 集合元素的顺序流
    // @since 1.8
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }

    // 返回一个可能的并行流 {@code Stream}，该方法允许返回顺序流
    // 这个方法应该被重写 当 {@link #spliterator()} 方法不能返回 spliterator
    // @return 一个可能的并行流
    // @since 1.8
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
}

```
