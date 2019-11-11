---
layout:     post
title:      LinkedHashMap
subtitle:   LinkedHashMap
date:       2019-11-11
author:     渣子哥
header-img: img/pexels-photo-1936936.jpeg
catalog: true
tags:
    - java8
    - JDK源码
    - 数据结构与算法
---


# Summary
```java
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V>
```

哈希表和链表实现了Map接口，具有可预测的迭代顺序。这个实现与HashMap的不同之处在于，它维护一个运行于所有条目中的双链表。这个链表定义了迭代顺序，这通常是键插入到映射中的顺序(插入顺序)。注意，如果一个键被重新插入到映射中，插入顺序不会受到影响。(key k被重新插入到map m中。当m.containsKey(k)在调用之前立即返回true时调用put(k, v)。

这个实现使它的客户端免受HashMap(和Hashtable)提供的未指定的、通常混乱的排序的影响，而不会增加与TreeMap相关的成本。它可以用来生成与原始地图顺序相同的地图的副本，而不管原始地图的实现是什么:
```java
     void foo(Map m) {
         Map copy = new LinkedHashMap(m);
         ...
     }
```
如果模块接受输入的映射并复制它，然后返回由复制的顺序决定的结果，那么这种技术特别有用。(客户通常很喜欢按照展示物品的顺序退货。)

提供了一个特殊的构造函数来创建一个链接的散列映射，其迭代顺序是其条目最后一次访问的顺序，从最近访问到最近访问(访问顺序)。这种地图非常适合构建LRU缓存。调用put、putIfAbsent、get、getOrDefault、compute、computeIfAbsent、computeIfPresent或merge方法会导致对相应条目的访问(假设在调用完成后存在)。如果值被替换，则replace方法只会导致对条目的访问。putAll方法为指定映射中的每个映射生成一个条目访问，其顺序是键值映射由指定映射的条目集迭代器提供。没有其他方法生成条目访问。特别是，对集合视图的操作不会影响支持映射的迭代顺序。

该类提供所有可选的映射操作，并允许空元素。与HashMap一样，它为基本操作(添加、包含和删除)提供了固定时间的性能，假设hash函数正确地将元素分散到各个桶中。由于维护链表的额外开销，性能可能略低于HashMap，但有一个例外:迭代LinkedHashMap的集合视图需要与映射大小成比例的时间，而不管其容量如何。HashMap上的迭代可能更昂贵，需要与它的容量成比例的时间。

链接哈希映射有两个影响其性能的参数:初始容量和负载因子。它们被精确地定义为HashMap。但是，请注意，对于初始容量选择过高的值，这个类的惩罚没有HashMap那么严重，因为这个类的迭代时间不受容量的影响。

注意，这个实现不是同步的。如果多个线程同时访问一个链接的散列映射，并且其中至少有一个线程从结构上修改了映射，那么它必须在外部同步。这通常是通过在一些自然封装映射的对象上进行同步来实现的。如果不存在这样的对象，则应该使用集合“包装”映射。synchronizedMap方法。这最好在创建时完成，以防止意外的不同步访问地图:
```java
   Map m = Collections.synchronizedMap(new LinkedHashMap(...));
```
结构修改是添加或删除一个或多个映射的任何操作，或者在访问顺序链接哈希映射的情况下，影响迭代顺序。在插入顺序链接哈希映射中，仅更改与映射中已包含的键关联的值不是结构性修改。在访问顺序链接哈希映射中，仅使用get查询映射是一种结构修改。

返回的迭代器返回的集合的迭代器方法这门课的所有集合视图方法快速失败:如果结构修改地图创建迭代器后,任何时候以任何方式除非通过迭代器的删除方法,迭代器将抛出ConcurrentModificationException。因此，在面对并发修改时，迭代器会快速而干净地失败，而不是在将来某个不确定的时间冒着任意的、不确定的行为的风险。

注意，不能保证迭代器的快速故障行为，因为通常来说，在存在非同步并发修改的情况下，不可能做出任何严格的保证。故障快速迭代器以最大的努力抛出ConcurrentModificationException。因此，编写一个依赖于此异常来判断其正确性的程序是错误的:迭代器的快速故障行为应该只用于检测bug。

这个类的所有集合视图方法返回的集合的spliterator方法返回的spliterator是延迟绑定的、故障快速的，另外还有report spliterator . ordered。

该类是Java集合框架的成员。
### Since 1.4






# Class
## Entry
```java
    static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }
```
继承自HashMap.Node，多了 before, after；






# Attribute
```java
private static final long serialVersionUID = 3801124242820219131L;
```



```java
transient LinkedHashMap.Entry<K,V> head;
```
双链表的头(最年长的)。




```java
    transient LinkedHashMap.Entry<K,V> tail;
```
双链表中最年轻的。




```java
    final boolean accessOrder;
```
这个链接哈希映射的迭代排序方法:<tt>true</tt> for access-order， <tt>false</tt> for insert -order。
访问顺序还是插入顺序





# Method(internal utilities)
## linkNodeLast
```java
    // link at the end of list
    private void linkNodeLast(LinkedHashMap.Entry<K,V> p) {
        LinkedHashMap.Entry<K,V> last = tail;
        tail = p; // 尾变成p
        if (last == null) // 如果就这一个元素，那头尾就都是一个
            head = p;
        else {
            p.before = last;
            last.after = p;
        }
    }
```
链接到列表末尾








## transferLinks
```java
    // apply src's links to dst
    private void transferLinks(LinkedHashMap.Entry<K,V> src,
                               LinkedHashMap.Entry<K,V> dst) {
        LinkedHashMap.Entry<K,V> b = dst.before = src.before;
        LinkedHashMap.Entry<K,V> a = dst.after = src.after;
        if (b == null)
            head = dst;
        else
            b.after = dst;
        if (a == null)
            tail = dst;
        else
            a.before = dst;
    }
```
用dst替换src





# overrides of HashMap hook methods
## reinitialize
```java
    void reinitialize() {
        super.reinitialize();
        head = tail = null;
    }
```
清空






## newNode
```java
    Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {
        LinkedHashMap.Entry<K,V> p =
            new LinkedHashMap.Entry<K,V>(hash, key, value, e);
        linkNodeLast(p);
        return p;
    }
```
新增节点，并且放在链表后面，返回节点







## replacementNode
```java
    Node<K,V> replacementNode(Node<K,V> p, Node<K,V> next) {
        LinkedHashMap.Entry<K,V> q = (LinkedHashMap.Entry<K,V>)p;
        LinkedHashMap.Entry<K,V> t =
            new LinkedHashMap.Entry<K,V>(q.hash, q.key, q.value, next);
        transferLinks(q, t);
        return t;
    }
```
把q换成t







## newTreeNode
```java
    TreeNode<K,V> newTreeNode(int hash, K key, V value, Node<K,V> next) {
        TreeNode<K,V> p = new TreeNode<K,V>(hash, key, value, next);
        linkNodeLast(p);
        return p;
    }
```
新增树节点








## replacementTreeNode
```java
    TreeNode<K,V> replacementTreeNode(Node<K,V> p, Node<K,V> next) {
        LinkedHashMap.Entry<K,V> q = (LinkedHashMap.Entry<K,V>)p;
        TreeNode<K,V> t = new TreeNode<K,V>(q.hash, q.key, q.value, next);
        transferLinks(q, t);
        return t;
    }
```
替换树节点






## afterNodeRemoval
```java
    void afterNodeRemoval(Node<K,V> e) { // unlink
        LinkedHashMap.Entry<K,V> p =
            (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
        p.before = p.after = null;
        if (b == null)
            head = a;
        else
            b.after = a;
        if (a == null)
            tail = b;
        else
            a.before = b;
    }
```
移除节点







## afterNodeInsertion
```java
    void afterNodeInsertion(boolean evict) { // possibly remove eldest
        LinkedHashMap.Entry<K,V> first;
        if (evict && (first = head) != null && removeEldestEntry(first)) {
            K key = first.key;
            removeNode(hash(key), key, null, false, true);
        }
    }
```








## afterNodeAccess
```java
    void afterNodeAccess(Node<K,V> e) { // move node to last
        LinkedHashMap.Entry<K,V> last;
        if (accessOrder && (last = tail) != e) {
            LinkedHashMap.Entry<K,V> p =
                (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
            p.after = null;
            if (b == null)
                head = a;
            else
                b.after = a;
            if (a != null)
                a.before = b;
            else
                last = b;
            if (last == null)
                head = p;
            else {
                p.before = last;
                last.after = p;
            }
            tail = p;
            ++modCount;
        }
    }
```





## internalWriteEntries
```java
    void internalWriteEntries(java.io.ObjectOutputStream s) throws IOException {
        for (LinkedHashMap.Entry<K,V> e = head; e != null; e = e.after) {
            s.writeObject(e.key);
            s.writeObject(e.value);
        }
    }
```






## LinkedHashMap
```java
    public LinkedHashMap(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor);
        accessOrder = false;
    }
```
构造一个具有指定初始容量和负载因子的空插入顺序LinkedHashMap实例。






## LinkedHashMap
```java
    public LinkedHashMap(int initialCapacity) {
        super(initialCapacity);
        accessOrder = false;
    }
```
构造一个具有指定初始容量和默认加载因子(0.75)的空插入顺序LinkedHashMap实例。





## LinkedHashMap
```java
    public LinkedHashMap() {
        super();
        accessOrder = false;
    }
```
构造一个空的插入顺序LinkedHashMap实例，该实例具有默认的初始容量(16)和负载因子(0.75)。







## LinkedHashMap
```java
    public LinkedHashMap(Map<? extends K, ? extends V> m) {
        super();
        accessOrder = false;
        putMapEntries(m, false);
    }
```
使用与指定映射相同的映射构造按插入顺序排列的LinkedHashMap实例。LinkedHashMap实例使用默认的负载因子(0.75)和足够容纳指定映射的初始容量创建。





## LinkedHashMap
```java
    public LinkedHashMap(int initialCapacity,
                         float loadFactor,
                         boolean accessOrder) {
        super(initialCapacity, loadFactor);
        this.accessOrder = accessOrder;
    }
```
构造一个空的LinkedHashMap实例，该实例具有指定的初始容量、负载因子和排序模式。






## containsValue
```java
    public boolean containsValue(Object value) {
        for (LinkedHashMap.Entry<K,V> e = head; e != null; e = e.after) {
            V v = e.value;
            if (v == value || (value != null && value.equals(v)))
                return true;
        }
        return false;
    }
```
如果此映射将一个或多个键映射到指定值，则返回true。







## get
```java
    public V get(Object key) {
        Node<K,V> e;
        if ((e = getNode(hash(key), key)) == null)
            return null;
        if (accessOrder)
            afterNodeAccess(e);
        return e.value;
    }
```
返回指定键映射到的值，如果该映射不包含键的映射，则返回null。

更正式地说，如果这个映射包含从键k到值v的映射(key==null ?k==null: key.equals(k))，则该方法返回v;否则返回null。(最多可以有一个这样的映射。)

返回值为null并不一定表示映射不包含键的映射;也有可能映射显式地将键映射为null。containsKey操作可用于区分这两种情况。







## getOrDefault
```java
    public V getOrDefault(Object key, V defaultValue) {
       Node<K,V> e;
       if ((e = getNode(hash(key), key)) == null)
           return defaultValue;
       if (accessOrder)
           afterNodeAccess(e);
       return e.value;
   }
```
返回指定键映射到的值，如果此映射不包含键的映射，则返回defaultValue。









## clear
```java
    public void clear() {
        super.clear();
        head = tail = null;
    }
```
从该映射中删除所有映射。这个调用返回后映射将为空。








## removeEldestEntry
```java
    protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
        return false;
    }
```
如果此map应删除其最老的条目，则返回true。在映射中插入新条目后，put和putAll调用此方法。它为实现者提供了在每次添加新条目时删除最老条目的机会。如果映射表示缓存，这很有用:它允许映射通过删除陈旧的条目来减少内存消耗。

示例使用:此覆盖将允许映射增长到最多100个条目，然后在每次添加新条目时删除最老的条目，保持稳定状态为100个条目。
```java
     private static final int MAX_ENTRIES = 100;

     protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > MAX_ENTRIES;
     }
```
这种方法通常不以任何方式修改映射，而是允许映射按照其返回值的方向修改自身。允许此方法直接修改映射，但如果它这样做，则必须返回false(指示映射不应尝试任何进一步修改)。从该方法中修改映射后返回true的效果未指定。

此实现只返回false(因此此映射的行为类似于法线映射——不会删除最年长的元素)。









## keySet
```java
    public Set<K> keySet() {
        Set<K> ks = keySet;
        if (ks == null) {
            ks = new LinkedKeySet();
            keySet = ks;
        }
        return ks;
    }
    
    final class LinkedKeySet extends AbstractSet<K> {
        public final int size()                 { return size; }
        public final void clear()               { LinkedHashMap.this.clear(); }
        public final Iterator<K> iterator() {
            return new LinkedKeyIterator();
        }
        public final boolean contains(Object o) { return containsKey(o); }
        public final boolean remove(Object key) {
            return removeNode(hash(key), key, null, false, true) != null;
        }
        public final Spliterator<K> spliterator()  {
            return Spliterators.spliterator(this, Spliterator.SIZED |
                                            Spliterator.ORDERED |
                                            Spliterator.DISTINCT);
        }
        public final void forEach(Consumer<? super K> action) {
            if (action == null)
                throw new NullPointerException();
            int mc = modCount;
            for (LinkedHashMap.Entry<K,V> e = head; e != null; e = e.after)
                action.accept(e.key);
            if (modCount != mc)
                throw new ConcurrentModificationException();
        }
    }
```
返回此映射中包含的键的集合视图。集合由映射支持，因此对映射的更改反映在集合中，反之亦然。如果在对集合进行迭代时修改映射(除了通过迭代器自己的删除操作)，迭代的结果是未定义的。集合支持元素删除，元素删除通过迭代器从映射中删除对应的映射。它不支持add或addAll操作。它的Spliterator通常提供更快的顺序性能，但并行性能比HashMap差得多。








## values
```java
    public Collection<V> values() {
        Collection<V> vs = values;
        if (vs == null) {
            vs = new LinkedValues();
            values = vs;
        }
        return vs;
    }

    final class LinkedValues extends AbstractCollection<V> {
        public final int size()                 { return size; }
        public final void clear()               { LinkedHashMap.this.clear(); }
        public final Iterator<V> iterator() {
            return new LinkedValueIterator();
        }
        public final boolean contains(Object o) { return containsValue(o); }
        public final Spliterator<V> spliterator() {
            return Spliterators.spliterator(this, Spliterator.SIZED |
                                            Spliterator.ORDERED);
        }
        public final void forEach(Consumer<? super V> action) {
            if (action == null)
                throw new NullPointerException();
            int mc = modCount;
            for (LinkedHashMap.Entry<K,V> e = head; e != null; e = e.after)
                action.accept(e.value);
            if (modCount != mc)
                throw new ConcurrentModificationException();
        }
    }
```
返回此映射中包含的值的集合视图。集合由映射支持，因此对映射的更改将反映在集合中，反之亦然。如果在对集合进行迭代时修改映射(除了通过迭代器自己的删除操作)，迭代的结果是未定义的。集合支持元素删除，元素删除通过迭代器从映射中删除对应的映射。它不支持add或addAll操作。它的Spliterator通常提供更快的顺序性能，但并行性能比HashMap差得多。









## entrySet
```java
    public Set<Map.Entry<K,V>> entrySet() {
        Set<Map.Entry<K,V>> es;
        return (es = entrySet) == null ? (entrySet = new LinkedEntrySet()) : es;
    }

    final class LinkedEntrySet extends AbstractSet<Map.Entry<K,V>> {
        public final int size()                 { return size; }
        public final void clear()               { LinkedHashMap.this.clear(); }
        public final Iterator<Map.Entry<K,V>> iterator() {
            return new LinkedEntryIterator();
        }
        public final boolean contains(Object o) {
            if (!(o instanceof Map.Entry))
                return false;
            Map.Entry<?,?> e = (Map.Entry<?,?>) o;
            Object key = e.getKey();
            Node<K,V> candidate = getNode(hash(key), key);
            return candidate != null && candidate.equals(e);
        }
        public final boolean remove(Object o) {
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>) o;
                Object key = e.getKey();
                Object value = e.getValue();
                return removeNode(hash(key), key, value, true, true) != null;
            }
            return false;
        }
        public final Spliterator<Map.Entry<K,V>> spliterator() {
            return Spliterators.spliterator(this, Spliterator.SIZED |
                                            Spliterator.ORDERED |
                                            Spliterator.DISTINCT);
        }
        public final void forEach(Consumer<? super Map.Entry<K,V>> action) {
            if (action == null)
                throw new NullPointerException();
            int mc = modCount;
            for (LinkedHashMap.Entry<K,V> e = head; e != null; e = e.after)
                action.accept(e);
            if (modCount != mc)
                throw new ConcurrentModificationException();
        }
    }
```
返回此映射中包含的映射的集合视图。集合由映射支持，因此对映射的更改反映在集合中，反之亦然。如果在对集合进行迭代时修改映射(除了通过迭代器自己的删除操作，或者通过迭代器返回的映射条目上的setValue操作)，迭代的结果是未定义的。集合支持元素删除，元素删除通过迭代器从映射中删除对应的映射。它的Spliterator通常提供更快的顺序性能，但并行性能比HashMap差得多。







# Map overrides method
## forEach
```java
    public void forEach(BiConsumer<? super K, ? super V> action) {
        if (action == null)
            throw new NullPointerException();
        int mc = modCount;
        for (LinkedHashMap.Entry<K,V> e = head; e != null; e = e.after)
            action.accept(e.key, e.value);
        if (modCount != mc)
            throw new ConcurrentModificationException();
    }
```
为映射中的每个条目执行给定的操作，直到处理完所有条目或操作引发异常为止。除非实现类另有指定，否则操作将按照条目集迭代的顺序执行(如果指定了迭代顺序)。该操作引发的异常将传递给调用者。









## replaceAll
```java
    public void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) {
        if (function == null)
            throw new NullPointerException();
        int mc = modCount;
        for (LinkedHashMap.Entry<K,V> e = head; e != null; e = e.after)
            e.value = function.apply(e.key, e.value);
        if (modCount != mc)
            throw new ConcurrentModificationException();
    }
```
将每个条目的值替换为对该条目调用给定函数的结果，直到所有条目都被处理或函数抛出异常。函数抛出的异常将传递给调用者。








## Iterators
```java

    abstract class LinkedHashIterator {
        LinkedHashMap.Entry<K,V> next;
        LinkedHashMap.Entry<K,V> current;
        int expectedModCount;

        LinkedHashIterator() {
            next = head;
            expectedModCount = modCount;
            current = null;
        }

        public final boolean hasNext() {
            return next != null;
        }

        final LinkedHashMap.Entry<K,V> nextNode() {
            LinkedHashMap.Entry<K,V> e = next;
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
            if (e == null)
                throw new NoSuchElementException();
            current = e;
            next = e.after;
            return e;
        }

        public final void remove() {
            Node<K,V> p = current;
            if (p == null)
                throw new IllegalStateException();
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
            current = null;
            K key = p.key;
            removeNode(hash(key), key, null, false, false);
            expectedModCount = modCount;
        }
    }

    final class LinkedKeyIterator extends LinkedHashIterator
        implements Iterator<K> {
        public final K next() { return nextNode().getKey(); }
    }

    final class LinkedValueIterator extends LinkedHashIterator
        implements Iterator<V> {
        public final V next() { return nextNode().value; }
    }

    final class LinkedEntryIterator extends LinkedHashIterator
        implements Iterator<Map.Entry<K,V>> {
        public final Map.Entry<K,V> next() { return nextNode(); }
    }


```

