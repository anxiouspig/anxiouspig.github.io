---
layout:     post
title:      PriorityQueue
subtitle:   PriorityQueue
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
public class PriorityQueue<E> extends AbstractQueue<E>
    implements java.io.Serializable
```
基于优先级堆的无界优先级队列。优先队列的元素是根据它们的自然顺序排序的，或者由队列构建时提供的比较器排序，这取决于使用的是哪个构造函数。优先队列不允许空元素。依赖于自然排序的优先队列也不允许插入不可比较的对象(这样做可能导致ClassCastException)。

这个队列的头是与指定顺序相关的最小元素。如果多个元素以最小值绑定，则head就是这些元素之一 - 绑定是任意断开的。队列检索操作轮询、删除、查看和元素访问队列头部的元素。

优先级队列是无界的，但具有控制数组大小的内部容量，数组用于存储队列中的元素。它总是至少和队列大小一样大。当元素被添加到优先队列时，它的容量会自动增长。增长策略的细节没有指定。

该类及其迭代器实现集合和迭代器接口的所有可选方法。方法Iterator()中提供的迭代器不能保证以任何特定的顺序遍历优先级队列的元素。如果需要有序遍历，可以考虑使用array.sort(pq.toArray())。

注意，这个实现不是同步的。如果任何线程修改队列，多线程不应该同时访问PriorityQueue实例。相反，使用线程安全的PriorityBlockingQueue类。

实现注意:此实现为入、退队列方法(offer、poll、remove()和add)提供O(log(n))时间;删除(对象)和包含(对象)方法的线性时间;以及检索方法的常数时间(peek、元素和大小)。

该类是Java集合框架的成员。
### Type Param
E - 此集合中包含的元素的类型











# Attribute
```java
    private static final long serialVersionUID = -7720805057305804111L;

    private static final int DEFAULT_INITIAL_CAPACITY = 11;
```
默认初始化容量




```java
    transient Object[] queue; // non-private to simplify nested class access
```
优先级队列表示为一个平衡的二进制堆:队列[n]的两个子队列[2 * n+1]和队列[2 * (n+1)]。如果comparator为空，则优先级队列按比较器排序，或者按元素的自然顺序排序:对于堆中的每个节点n和n的每个后代d, n <= d。




```java
    private int size = 0;
```
优先队列中元素的数量。



```java
    private final Comparator<? super E> comparator;
```
如果优先级队列使用元素的自然顺序，则为null。




```java
    transient int modCount = 0; // non-private to simplify nested class access
```
这个优先队列在结构上修改了<i> structurally modified </i>的次数。有关细节，请参见AbstractList。





# Constructor
```java
    public PriorityQueue() {
        this(DEFAULT_INITIAL_CAPACITY, null);
    }
```
创建一个具有默认初始容量(11)的PriorityQueue，该队列根据元素的自然顺序对元素排序。





```java
    public PriorityQueue(int initialCapacity) {
        this(initialCapacity, null);
    }
```
创建一个具有指定初始容量的PriorityQueue，该初始容量根据元素的自然顺序对其进行排序。
### Param
initialCapacity - 此优先级队列的初始容量




```java
    public PriorityQueue(Comparator<? super E> comparator) {
        this(DEFAULT_INITIAL_CAPACITY, comparator);
    }
```
创建具有默认初始容量的PriorityQueue，其元素按照指定的比较器排序。
### Param
comparator - 将用于对该优先级队列排序的比较器。如果为空，则使用元素的自然顺序。





```java
    public PriorityQueue(int initialCapacity,
                         Comparator<? super E> comparator) {
        // Note: This restriction of at least one is not actually needed,
        // but continues for 1.5 compatibility
        if (initialCapacity < 1)
            throw new IllegalArgumentException();
        this.queue = new Object[initialCapacity];
        this.comparator = comparator;
    }
```
创建具有指定初始容量的PriorityQueue，该队列根据指定的比较器对其元素进行排序。
### Param
initialCapacity - 此优先级队列的初始容量
comparator - 将用于对该优先级队列排序的比较器。如果为空，则使用元素的自然顺序。





```java
    @SuppressWarnings("unchecked")
    public PriorityQueue(Collection<? extends E> c) {
        if (c instanceof SortedSet<?>) {
            SortedSet<? extends E> ss = (SortedSet<? extends E>) c;
            this.comparator = (Comparator<? super E>) ss.comparator();
            initElementsFromCollection(ss);
        }
        else if (c instanceof PriorityQueue<?>) {
            PriorityQueue<? extends E> pq = (PriorityQueue<? extends E>) c;
            this.comparator = (Comparator<? super E>) pq.comparator();
            initFromPriorityQueue(pq);
        }
        else {
            this.comparator = null;
            initFromCollection(c);
        }
    }
```
创建包含指定集合中的元素的PriorityQueue。如果指定的集合是SortedSet的实例，或者是另一个PriorityQueue，那么这个优先队列将按照相同的顺序排序。否则，该优先队列将根据其元素的自然顺序排序。
### Param
c - 要将其元素放入此优先级队列的集合





```java
    @SuppressWarnings("unchecked")
    public PriorityQueue(PriorityQueue<? extends E> c) {
        this.comparator = (Comparator<? super E>) c.comparator();
        initFromPriorityQueue(c);
    }
```
创建一个包含指定优先级队列中的元素的PriorityQueue。这个优先队列将按照与给定优先队列相同的顺序排序。
### Param
c - 优先级队列，其元素将被放入此优先级队列




```java
    @SuppressWarnings("unchecked")
    public PriorityQueue(SortedSet<? extends E> c) {
        this.comparator = (Comparator<? super E>) c.comparator();
        initElementsFromCollection(c);
    }
```
创建一个包含指定排序集中元素的优先队列。这个优先队列将按照与给定排序集相同的顺序排序。
### Param
c - 将其元素放入此优先队列的已排序集合






# Method
## initFromPriorityQueue
```java
    private void initFromPriorityQueue(PriorityQueue<? extends E> c) {
        if (c.getClass() == PriorityQueue.class) {
            this.queue = c.toArray();
            this.size = c.size();
        } else {
            initFromCollection(c);
        }
    }
```
将指定的元素插入此优先队列。
### Param
e - 要添加的元素
### Return
true(由Collection.add(E)指定) 









## initElementsFromCollection
```java
    private void initElementsFromCollection(Collection<? extends E> c) {
        Object[] a = c.toArray();
        // If c.toArray incorrectly doesn't return Object[], copy it.
        if (a.getClass() != Object[].class)
            a = Arrays.copyOf(a, a.length, Object[].class);
        int len = a.length;
        if (len == 1 || this.comparator != null)
            for (int i = 0; i < len; i++)
                if (a[i] == null)
                    throw new NullPointerException();
        this.queue = a;
        this.size = a.length;
    }
```
初始化集合表

传进来的都是排序的





## initFromCollection
```java
    private void initFromCollection(Collection<? extends E> c) {
        initElementsFromCollection(c);
        heapify();
    }
```
排序操作
使用给定集合中的元素初始化队列数组。
### Param
c - the collection





## grow
```java
    // 要分配的数组的最大大小。一些vm在数组中保留一些头信息。
    // 试图分配更大的数组可能会导致OutOfMemoryError:请求的数组大小超过VM限制
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    /**
     * 增加数组的容量。
     *
     * @param minCapacity 所需的最小容量
     */
    private void grow(int minCapacity) {
        int oldCapacity = queue.length;
        // Double size if small; else grow by 50%
        int newCapacity = oldCapacity + ((oldCapacity < 64) ?
                                         (oldCapacity + 2) :
                                         (oldCapacity >> 1));
        // overflow-conscious代码
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        queue = Arrays.copyOf(queue, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```
增加数组的容量。
### Param
minCapacity - 所需的最小容量






## add
```java
    public boolean add(E e) {
        return offer(e);
    }
```
将指定的元素插入此优先队列。
### Param
e - 添加的元素
### Return
true(由Collection.add(E)指定)





## offer
```java
    public boolean offer(E e) {
        if (e == null)
            throw new NullPointerException();
        modCount++;
        int i = size;
        if (i >= queue.length)
            grow(i + 1);
        size = i + 1;
        if (i == 0)
            queue[0] = e;
        else
            siftUp(i, e);
        return true;
    }
```
将指定的元素插入此优先队列。
### Param
e - 添加的元素
### Return
true(由Queue.offer(E)指定)





## peek
```java
    @SuppressWarnings("unchecked")
    public E peek() {
        return (size == 0) ? null : (E) queue[0];
    }
```
检索但不删除此队列的头部，如果该队列为空，则返回null。
### Return
此队列的头，如果该队列为空，则为空






## remove
```java

    private int indexOf(Object o) {
        if (o != null) {
            for (int i = 0; i < size; i++)
                if (o.equals(queue[i]))
                    return i;
        }
        return -1;
    }
    
    public boolean remove(Object o) {
        int i = indexOf(o);
        if (i == -1)
            return false;
        else {
            removeAt(i);
            return true;
        }
    }
```
从此队列中删除指定元素的单个实例(如果存在)。更正式地说，如果队列包含一个或多个这样的元素，则删除一个元素e，使o.equals(e)。仅当且仅当此队列包含指定的元素时返回true(或者等效地，如果此队列由于调用而更改)。
### Param
o - 要从此队列中删除的元素(如果存在)
### Return
如果此队列因调用而更改，则为true






## removeEq
```java
    boolean removeEq(Object o) {
        for (int i = 0; i < size; i++) {
            if (o == queue[i]) {
                removeAt(i);
                return true;
            }
        }
        return false;
    }
```
删除所有相同元素






## contains
```java
    public boolean contains(Object o) {
        return indexOf(o) != -1;
    }
```
如果此队列包含指定的元素，则返回true。更正式地说，当且仅当此队列包含至少一个元素e，使得o.equals(e)时返回true。
### Param
o - 对象，以检查此队列中的包含
### Return
如果此队列包含指定的元素，则为true





## toArray
```java
    public Object[] toArray() {
        return Arrays.copyOf(queue, size);
    }
```
返回包含此队列中所有元素的数组。元素没有特定的顺序。

返回的数组将是“安全的”，因为这个队列不维护对它的引用。(换句话说，这个方法必须分配一个新的数组)。因此，调用者可以自由地修改返回的数组。

此方法充当基于数组和基于集合的api之间的桥梁。
### Return
包含此队列中所有元素的数组






## toArray
```java
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        final int size = this.size;
        if (a.length < size)
            // Make a new array of a's runtime type, but my contents:
            return (T[]) Arrays.copyOf(queue, size, a.getClass());
        System.arraycopy(queue, 0, a, 0, size);
        if (a.length > size)
            a[size] = null;
        return a;
    }
```
返回包含此队列中所有元素的数组;返回数组的运行时类型是指定数组的运行时类型。返回的数组元素没有特定的顺序。如果队列符合指定的数组，则返回其中的队列。否则，将使用指定数组的运行时类型和该队列的大小分配一个新数组。

如果队列符合指定的数组，则有剩余空间(即，数组中的元素比队列中的元素多)，集合结束后数组中的元素被设置为null。

与toArray()方法一样，该方法充当基于数组和基于集合的api之间的桥梁。此外，这种方法允许对输出数组的运行时类型进行精确控制，在某些情况下，还可以用来节省分配成本。
### Param
a - 如果队列的元素足够大，则将其存储到其中的数组;否则，将为此目的分配相同运行时类型的新数组。
### Return
包含此队列中所有元素的数组






## iterator
```java
    public Iterator<E> iterator() {
        return new Itr();
    }
    
    private final class Itr implements Iterator<E> {
        /**
         * 元素的索引(进入队列数组)，由后续调用next返回。
         */
        private int cursor = 0;

        /**
         * 最近一次调用next返回的元素的索引,
         * 除非该元素来自于 the forgetMeNot list.
         * 如果元素被要删除的调用删除，则将其设置为-1.
         */
        private int lastRet = -1;

        /**
         * 在迭代过程中，由于“不幸的”元素删除，从堆的未访问部分移动到访问部分的元素队列。
         * (不幸的是，删除元素需要进行筛选，而不是进行筛选。)
         * 我们必须访问列表中的所有元素来完成迭代。我们在完成“常规”迭代之后执行此操作。
         *
         * 我们期望大多数迭代，甚至包括那些涉及删除的迭代,
         * 是否不需要在该字段中存储元素.
         */
         // 删除的特殊情况，替代的节点会上移
        private ArrayDeque<E> forgetMeNot = null;

        /**
         * 最后最近一次调用next方法返回的元素 
         */
        private E lastRetElt = null;

        /**
         * 迭代器认为备份队列应该具有的modCount值。如果违背了这个期望，迭代器将检测到并发修改。
         */
        private int expectedModCount = modCount;

        public boolean hasNext() {
            return cursor < size ||
                (forgetMeNot != null && !forgetMeNot.isEmpty());
        }

        @SuppressWarnings("unchecked")
        public E next() {
            if (expectedModCount != modCount)
                throw new ConcurrentModificationException();
            if (cursor < size)
                return (E) queue[lastRet = cursor++];
            if (forgetMeNot != null) {
                lastRet = -1;
                // 取出上移未遍历的节点
                lastRetElt = forgetMeNot.poll();
                if (lastRetElt != null)
                    return lastRetElt;
            }
            throw new NoSuchElementException();
        }

        public void remove() {
            if (expectedModCount != modCount)
                throw new ConcurrentModificationException();
            if (lastRet != -1) {
                E moved = PriorityQueue.this.removeAt(lastRet);
                lastRet = -1;
                if (moved == null)
                    cursor--;
                else {
                // 删除的节点上移的情况
                    if (forgetMeNot == null)
                        forgetMeNot = new ArrayDeque<>();
                    forgetMeNot.add(moved);
                }
            } else if (lastRetElt != null) {
                PriorityQueue.this.removeEq(lastRetElt);
                lastRetElt = null;
            } else {
                throw new IllegalStateException();
            }
            expectedModCount = modCount;
        }
    }
```
返回此队列中元素的迭代器。迭代器不会以任何特定的顺序返回元素。
### Return
此队列中的元素上的迭代器







## size
```java
    public int size() {
        return size;
    }
```
返回此集合中的元素数量。如果此集合包含多个整数。MAX_VALUE元素，返回Integer.MAX_VALUE。
### Return
此集合中元素的数量







## clear
```java
    public void clear() {
        modCount++;
        for (int i = 0; i < size; i++)
            queue[i] = null;
        size = 0;
    }
```
从该优先级队列中删除所有元素。此调用返回后，队列将为空。





## poll
```java
    @SuppressWarnings("unchecked")
    public E poll() {
        if (size == 0)
            return null;
        int s = --size;
        modCount++;
        E result = (E) queue[0];
        E x = (E) queue[s];
        queue[s] = null;
        if (s != 0)
            siftDown(0, x);
        return result;
    }
```
检索并删除此队列的头部，如果该队列为空，则返回null。
### Return
此队列的头，如果该队列为空，则为空






## removeAt
```java
    @SuppressWarnings("unchecked")
    private E removeAt(int i) {
        // assert i >= 0 && i < size;
        modCount++;
        int s = --size;
        if (s == i) // removed last element
            queue[i] = null;
        else {
            E moved = (E) queue[s];
            queue[s] = null;
            siftDown(i, moved);
            if (queue[i] == moved) {
                siftUp(i, moved);
                if (queue[i] != moved)
                    return moved;
            }
        }
        return null;
    }
```
从队列中删除第i个元素。

通常，这种方法会保留i-1以下的元素(包括所有元素)。在这些情况下，它返回null。有时候,为了保持堆不变,必须交换后列表的元素有一个比我早。在这种情况下,该方法返回之前的元素列表的最后,现在我之前在一些位置。这一事实是由迭代器使用。删除，以避免丢失遍历元素。








## siftUp
```java
    private void siftUp(int k, E x) {
        if (comparator != null)
            siftUpUsingComparator(k, x);
        else
            siftUpComparable(k, x);
    }

    @SuppressWarnings("unchecked")
    private void siftUpComparable(int k, E x) {
        Comparable<? super E> key = (Comparable<? super E>) x;
        while (k > 0) {
            int parent = (k - 1) >>> 1;
            Object e = queue[parent];
            if (key.compareTo((E) e) >= 0)
                break;
            queue[k] = e;
            k = parent;
        }
        queue[k] = key;
    }

    @SuppressWarnings("unchecked")
    private void siftUpUsingComparator(int k, E x) {
        while (k > 0) {
            int parent = (k - 1) >>> 1;
            Object e = queue[parent];
            if (comparator.compare(x, (E) e) >= 0)
                break;
            queue[k] = e;
            k = parent;
        }
        queue[k] = x;
    }
```
在k位置插入项x，通过向上提升x直到它大于或等于其父项或根项，从而保持堆不变。

简化和加速强制转换和比较。比较器和比较器版本被分成不同的方法，这些方法在其他方面是相同的。(类似siftDown。)
### Param
k - 需要填补的位置
x - 插入元素







## siftDown
```java
    private void siftDown(int k, E x) {
        if (comparator != null)
            siftDownUsingComparator(k, x);
        else
            siftDownComparable(k, x);
    }
    
    @SuppressWarnings("unchecked")
    private void siftDownComparable(int k, E x) {
        Comparable<? super E> key = (Comparable<? super E>)x;
        int half = size >>> 1;        // loop while a non-leaf
        while (k < half) {
            int child = (k << 1) + 1; // assume left child is least
            Object c = queue[child];
            int right = child + 1;
            if (right < size &&
                ((Comparable<? super E>) c).compareTo((E) queue[right]) > 0)
                c = queue[child = right];
            if (key.compareTo((E) c) <= 0)
                break;
            queue[k] = c;
            k = child;
        }
        queue[k] = key;
    }

    @SuppressWarnings("unchecked")
    private void siftDownUsingComparator(int k, E x) {
        int half = size >>> 1;
        while (k < half) {
            int child = (k << 1) + 1;
            Object c = queue[child];
            int right = child + 1;
            if (right < size &&
                comparator.compare((E) c, (E) queue[right]) > 0)
                c = queue[child = right];
            if (comparator.compare(x, (E) c) <= 0)
                break;
            queue[k] = c;
            k = child;
        }
        queue[k] = x;
    }

```
将项目x插入到k的位置，通过反复将x降级到树的下面，直到它小于或等于它的子元素或叶子，从而保持堆不变。
### Param
k - 需要填补的位置
x - 插入元素





## heapify
```java
    @SuppressWarnings("unchecked")
    private void heapify() {
        for (int i = (size >>> 1) - 1; i >= 0; i--)
            siftDown(i, (E) queue[i]);
    }
```
生成二叉堆操作







## comparator
```java
    public Comparator<? super E> comparator() {
        return comparator;
    }
```
返回用于对该队列中的元素排序的比较器，如果该队列按照其元素的自然顺序排序，则返回null。
### Return
用于对该队列排序的比较器，如果该队列按照其元素的自然顺序排序，则为null







## writeObject
```java
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException {
        // Write out element count, and any hidden stuff
        s.defaultWriteObject();

        // Write out array length, for compatibility with 1.5 version
        s.writeInt(Math.max(2, size + 1));

        // Write out all elements in the "proper order".
        for (int i = 0; i < size; i++)
            s.writeObject(queue[i]);
    }
```







## readObject
```java
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        // Read in size, and any hidden stuff
        s.defaultReadObject();

        // Read in (and discard) array length
        s.readInt();

        SharedSecrets.getJavaOISAccess().checkArray(s, Object[].class, size);
        queue = new Object[size];

        // Read in all elements.
        for (int i = 0; i < size; i++)
            queue[i] = s.readObject();

        // Elements are guaranteed to be in "proper order", but the
        // spec has never explained what that might be.
        heapify();
    }
```







## spliterator
```java
    public final Spliterator<E> spliterator() {
        return new PriorityQueueSpliterator<E>(this, 0, -1, 0);
    }

    static final class PriorityQueueSpliterator<E> implements Spliterator<E> {
        /*
         * This is very similar to ArrayList Spliterator, except for
         * extra null checks.
         */
        private final PriorityQueue<E> pq;
        private int index;            // current index, modified on advance/split
        private int fence;            // -1 until first use
        private int expectedModCount; // initialized when fence set

        /** Creates new spliterator covering the given range */
        PriorityQueueSpliterator(PriorityQueue<E> pq, int origin, int fence,
                             int expectedModCount) {
            this.pq = pq;
            this.index = origin;
            this.fence = fence;
            this.expectedModCount = expectedModCount;
        }

        private int getFence() { // initialize fence to size on first use
            int hi;
            if ((hi = fence) < 0) {
                expectedModCount = pq.modCount;
                hi = fence = pq.size;
            }
            return hi;
        }

        public PriorityQueueSpliterator<E> trySplit() {
            int hi = getFence(), lo = index, mid = (lo + hi) >>> 1;
            return (lo >= mid) ? null :
                new PriorityQueueSpliterator<E>(pq, lo, index = mid,
                                                expectedModCount);
        }

        @SuppressWarnings("unchecked")
        public void forEachRemaining(Consumer<? super E> action) {
            int i, hi, mc; // hoist accesses and checks from loop
            PriorityQueue<E> q; Object[] a;
            if (action == null)
                throw new NullPointerException();
            if ((q = pq) != null && (a = q.queue) != null) {
                if ((hi = fence) < 0) {
                    mc = q.modCount;
                    hi = q.size;
                }
                else
                    mc = expectedModCount;
                if ((i = index) >= 0 && (index = hi) <= a.length) {
                    for (E e;; ++i) {
                        if (i < hi) {
                            if ((e = (E) a[i]) == null) // must be CME
                                break;
                            action.accept(e);
                        }
                        else if (q.modCount != mc)
                            break;
                        else
                            return;
                    }
                }
            }
            throw new ConcurrentModificationException();
        }

        public boolean tryAdvance(Consumer<? super E> action) {
            if (action == null)
                throw new NullPointerException();
            int hi = getFence(), lo = index;
            if (lo >= 0 && lo < hi) {
                index = lo + 1;
                @SuppressWarnings("unchecked") E e = (E)pq.queue[lo];
                if (e == null)
                    throw new ConcurrentModificationException();
                action.accept(e);
                if (pq.modCount != expectedModCount)
                    throw new ConcurrentModificationException();
                return true;
            }
            return false;
        }

        public long estimateSize() {
            return (long) (getFence() - index);
        }

        public int characteristics() {
            return Spliterator.SIZED | Spliterator.SUBSIZED | Spliterator.NONNULL;
        }
    }
```
在此队列中的元素上创建延迟绑定和故障快速Spliterator。
Spliterator报告Spliterator。大小,Spliterator。小尺寸,Spliterator.NONNULL。覆盖实现应该记录额外特征值的报告。