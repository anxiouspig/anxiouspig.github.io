
---
layout:     post
title:      ArrayDeque
subtitle:   ArrayDeque
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
public class ArrayDeque<E> extends AbstractCollection<E>
                           implements Deque<E>, Cloneable, Serializable
```
Deque接口的可调整数组实现。阵列deques无容量限制;它们随着支持使用的需要而增长。它们不是线程安全的;在缺乏外部同步的情况下，它们不支持多线程的并发访问。禁止空元素。这个类用作堆栈时可能比Stack快，用作队列时可能比LinkedList快。

大多数矩阵运算都是在平摊常数时间内进行的。异常包括remove、removeFirstOccurrence、removeLastOccurrence、contains、iterator.remove()和bulk操作，所有这些操作都在线性时间内运行。

这个类的iterator方法返回的迭代器是快速故障的:如果在创建迭代器之后修改deque，除了通过迭代器自己的remove方法之外，其他任何方法都可以修改deque，迭代器通常会抛出ConcurrentModificationException。因此，在面对并发修改时，迭代器会快速而干净地失败，而不是在将来某个不确定的时间冒着任意的、不确定的行为的风险。

注意，不能保证迭代器的快速故障行为，因为通常来说，在存在非同步并发修改的情况下，不可能做出任何严格的保证。故障快速迭代器以最大的努力抛出ConcurrentModificationException。因此，编写一个依赖于此异常来判断其正确性的程序是错误的:迭代器的快速故障行为应该只用于检测bug。

该类及其迭代器实现集合和迭代器接口的所有可选方法。

该类是Java集合框架的成员。
### Type Parameter
E - 此集合中包含的元素的类型
### Since 1.6






# Attruibute
```java
    transient Object[] elements; // non-private to simplify nested class access
```
存储deque元素的数组。deque的容量是这个数组的长度，它总是2的幂。数组永远不允许变成满的，除非是在addX方法中，当数组变成满的时候，它会立即调整大小(请参阅doubleCapacity)，这样就避免了头和尾相互缠绕，使其相等。我们还保证所有不包含deque元素的数组单元格始终为空。




```java
    transient int head;
```
deque头部元素的索引(该元素将被remove()或pop()删除);如果deque是空的，则是一个任意的等于tail的数。





```java
    transient int tail;
```
将下一个元素添加到deque尾部的索引(通过addLast(E)、add(E)或push(E))。




```java
    private static final int MIN_INITIAL_CAPACITY = 8;
```
我们将为新创建的deque使用的最小容量。一定是2的幂。





# Method(Array allocation and resizing utilities)
## calculateSize
```java
    private static int calculateSize(int numElements) {
        int initialCapacity = MIN_INITIAL_CAPACITY; // 8
        // 找出两种元素的最大。
        // Tests "<=" because arrays aren't kept full.
        if (numElements >= initialCapacity) {
            initialCapacity = numElements;
            initialCapacity |= (initialCapacity >>>  1);
            initialCapacity |= (initialCapacity >>>  2);
            initialCapacity |= (initialCapacity >>>  4);
            initialCapacity |= (initialCapacity >>>  8);
            initialCapacity |= (initialCapacity >>> 16);
            initialCapacity++;
            // 变为2的次幂
            // 超过int最大,回退 2 ^ 30
            if (initialCapacity < 0)   // Too many elements, must back off
                initialCapacity >>>= 1;// Good luck allocating 2 ^ 30 elements
        }
        return initialCapacity;
    }
```
根据传入的int计算容量





## allocateElements
```java
    private void allocateElements(int numElements) {
        elements = new Object[calculateSize(numElements)];
    }
```
分配空数组以保存给定数量的元素。
### Param
numElements - 要保存的元素的数量






## doubleCapacity
```java
    private void doubleCapacity() {
        assert head == tail; // 循环链表，头等于尾
        int p = head; // 头索引
        int n = elements.length; // 数组容量
        int r = n - p; // p右边元素数量
        int newCapacity = n << 1; // 容量扩大二倍
        if (newCapacity < 0) // 容量过大
            throw new IllegalStateException("Sorry, deque too big");
        Object[] a = new Object[newCapacity];
        System.arraycopy(elements, p, a, 0, r);
        System.arraycopy(elements, 0, a, r, p);
        elements = a;
        head = 0;
        tail = n;
    }
```
两倍容量扩容





## copyElements
```java
    private <T> T[] copyElements(T[] a) {
        if (head < tail) { // 未循环情况
            System.arraycopy(elements, head, a, 0, size());
        } else if (head > tail) { // 循环情况
            int headPortionLen = elements.length - head;
            System.arraycopy(elements, head, a, 0, headPortionLen);
            System.arraycopy(elements, 0, a, headPortionLen, tail);
        }
        return a;
    }
```
复制元素






## ArrayDeque
```java
    public ArrayDeque() {
        elements = new Object[16];
    }
```
构造一个初始容量足够容纳16个元素的空数组deque。







## ArrayDeque
```java
    public ArrayDeque(int numElements) {
        allocateElements(numElements);
    }
```
构造一个空数组deque，其初始容量足以容纳指定数量的元素。
### Param
numElements - deque初始容量





## ArrayDeque
```java
    public ArrayDeque(Collection<? extends E> c) {
        allocateElements(c.size());
        addAll(c);
    }
```
构造一个deque，其中包含指定集合的元素，按照集合的迭代器返回元素的顺序。(集合的迭代器返回的第一个元素成为第一个元素，或deque的前面。)
### Param
c - 将其元素放入deque中的集合




## addFirst
```java
    public void addFirst(E e) {
        if (e == null)
            throw new NullPointerException();
        elements[head = (head - 1) & (elements.length - 1)] = e;
        if (head == tail)
            doubleCapacity();
    }
```
将指定的元素插入到此deque的前面。
### Param
e - 要添加的元素





## addLast
```java
    public void addLast(E e) {
        if (e == null)
            throw new NullPointerException();
        elements[tail] = e;
        if ( (tail = (tail + 1) & (elements.length - 1)) == head)
            doubleCapacity();
    }
```
在此deque的末尾插入指定的元素。

This method is equivalent to add(E).
## Param
e - 要添加的元素




## offerFirst
```java
    public boolean offerFirst(E e) {
        addFirst(e);
        return true;
    }
```
将指定的元素插入到此deque的前面。
### Param
e - 要添加的元素
### Returns
true(由Deque.offerFirst(E)指定)




## offerLast
```java
    public boolean offerLast(E e) {
        addLast(e);
        return true;
    }
```
在此deque的末尾插入指定的元素。
### Param
e - 要添加的元素
### Returns
true(由Deque.offerLast(E)指定)




## removeFirst
```java
    public E removeFirst() {
        E x = pollFirst();
        if (x == null)
            throw new NoSuchElementException();
        return x;
    }
```
检索并删除此deque的第一个元素。此方法与pollFirst的唯一不同之处在于，当deque为空时，它会抛出异常。
### Returns
此deque的第一个元素






## removeLast
```java
    public E removeLast() {
        E x = pollLast();
        if (x == null)
            throw new NoSuchElementException();
        return x;
    }
```
检索并删除此deque的最后一个元素。此方法与pollLast唯一的不同之处在于，当deque为空时，它会抛出异常。
### Returns
此deque的最后一个元素






## pollFirst
```java
    public E pollFirst() {
        int h = head;
        @SuppressWarnings("unchecked")
        E result = (E) elements[h];
        // Element is null if deque empty
        if (result == null)
            return null;
        elements[h] = null;     // Must null out slot
        head = (h + 1) & (elements.length - 1);
        return result;
    }
```
检索并删除此deque的第一个元素，如果该deque为空，则返回null。
### Returns
这个deque的头部，如果deque是空的，则为空







## pollLast
```java
    public E pollLast() {
        int t = (tail - 1) & (elements.length - 1);
        @SuppressWarnings("unchecked")
        E result = (E) elements[t];
        if (result == null)
            return null;
        elements[t] = null;
        tail = t;
        return result;
    }
```
检索并删除此deque的最后一个元素，如果该deque为空，则返回null。
### Returns
这个deque的尾部，如果这个deque是空的，则为null










## getFirst
```java
    public E getFirst() {
        @SuppressWarnings("unchecked")
        E result = (E) elements[head];
        if (result == null)
            throw new NoSuchElementException();
        return result;
    }
```
检索但不删除此deque的第一个元素。此方法与peekFirst的不同之处在于，它只在deque为空时抛出异常。
### Returns
此deque的第一个元素







## getLast
```java
    public E getLast() {
        @SuppressWarnings("unchecked")
        E result = (E) elements[(tail - 1) & (elements.length - 1)];
        if (result == null)
            throw new NoSuchElementException();
        return result;
    }
```
检索但不删除此deque的最后一个元素。此方法与peekLast的不同之处在于，它只在deque为空时抛出异常。
### Returns
此deque的最后一个元素





## peekFirst
```java
    @SuppressWarnings("unchecked")
    public E peekFirst() {
        // elements[head] is null if deque empty
        return (E) elements[head];
    }
```
检索但不删除此deque的第一个元素，如果该deque为空，则返回null。
### Returns
这个deque的头部，如果deque是空的，则为空







## peekLast
```java
    @SuppressWarnings("unchecked")
    public E peekLast() {
        return (E) elements[(tail - 1) & (elements.length - 1)];
    }
```
检索但不删除此deque的最后一个元素，如果该deque为空，则返回null。
### Returns
这个deque的尾部，如果这个deque是空的，则为null









## removeFirstOccurrence
```java
    public boolean removeFirstOccurrence(Object o) {
        if (o == null)
            return false;
        int mask = elements.length - 1;
        int i = head;
        Object x;
        while ( (x = elements[i]) != null) {
            if (o.equals(x)) {
                delete(i);
                return true;
            }
            i = (i + 1) & mask;
        }
        return false;
    }
```
删除此deque中指定元素的第一个出现项(当从头到尾遍历deque时)。如果deque不包含该元素，它将保持不变。更正式地说，删除第一个元素e，使o.equals(e)(如果存在这样一个元素)。如果这个deque包含指定的元素，则返回true(如果这个deque由于调用而更改，则返回true)。
### Param
o - 如果存在，则从该deque中删除元素
### Returns
如果deque包含指定的元素，则为true






## removeLastOccurrence
```java
    public boolean removeLastOccurrence(Object o) {
        if (o == null)
            return false;
        int mask = elements.length - 1;
        int i = (tail - 1) & mask;
        Object x;
        while ( (x = elements[i]) != null) {
            if (o.equals(x)) {
                delete(i);
                return true;
            }
            i = (i - 1) & mask;
        }
        return false;
    }
```
删除此deque中指定元素的最后一次出现(当从头到尾遍历deque时)。如果deque不包含该元素，它将保持不变。更正式地说，删除最后一个元素e，使o.equals(e)(如果存在这样一个元素)。如果这个deque包含指定的元素，则返回true(如果这个deque由于调用而更改，则返回true)。
### Param
o - 如果存在，则从该deque中删除元素
### Returns
如果deque包含指定的元素，则为true










## add
```java
    public boolean add(E e) {
        addLast(e);
        return true;
    }
```
在此deque的末尾插入指定的元素。
### Param
e - 要添加的元素
### Returns
true(由Collection.add(E)指定)





## offer
```java
    public boolean offer(E e) {
        return offerLast(e);
    }
```
在此deque的末尾插入指定的元素。
### Param
e - 要添加的元素
### Returns
true(由Queue.offer(E)指定)




## remove
```java
    public E remove() {
        return removeFirst();
    }
```
检索并删除此deque表示的队列的头部。此方法与poll的不同之处在于，它只在deque为空时抛出异常。

This method is equivalent to removeFirst().
### Returns
这个deque表示的队列的头





## poll
```java
    public E poll() {
        return pollFirst();
    }
```
检索并删除由这个deque表示的队列的头部(换句话说，就是这个deque的第一个元素)，如果这个deque为空，则返回null。
### Returns
此deque表示的队列的头部，如果该deque为空，则为null








## element
```java
    public E element() {
        return getFirst();
    }
```
检索但不删除此deque表示的队列的头部。这个方法与peek的不同之处在于，它只在deque为空时抛出异常。

This method is equivalent to getFirst().
### Returns
这个deque表示的队列的头





## peek
```java
    public E peek() {
        return peekFirst();
    }
```
检索但不删除此deque表示的队列的头部，如果该deque为空，则返回null。

This method is equivalent to peekFirst().
### Returns
此deque表示的队列的头部，如果该deque为空，则为null




# Stack methods
## push
```java
    public void push(E e) {
        addFirst(e);
    }
```
将元素推入由deque表示的堆栈。换句话说，插入deque前面的元素。
### Param
e - 要添加的元素









## pop
```java
    public E pop() {
        return removeFirst();
    }
```
从这个deque表示的堆栈中弹出一个元素。换句话说，删除并返回deque的第一个元素。
### Returns
deque前面的元素(deque表示堆栈的顶部)






## checkInvariants
```java
    private void checkInvariants() {
        assert elements[tail] == null;
        assert head == tail ? elements[head] == null :
            (elements[head] != null &&
             elements[(tail - 1) & (elements.length - 1)] != null);
        assert elements[(head - 1) & (elements.length - 1)] == null;
    }
```









## delete
```java
    private boolean delete(int i) {
        checkInvariants();
        final Object[] elements = this.elements;
        final int mask = elements.length - 1;
        final int h = head;
        final int t = tail;
        final int front = (i - h) & mask;
        final int back  = (t - i) & mask;

        // Invariant: head <= i < tail mod circularity
        if (front >= ((t - h) & mask))
            throw new ConcurrentModificationException();

        // Optimize for least element motion
        if (front < back) {
            if (h <= i) { // 这样复制少点
                System.arraycopy(elements, h, elements, h + 1, front);
            } else { // Wrap around
                System.arraycopy(elements, 0, elements, 1, i);
                elements[0] = elements[mask];
                System.arraycopy(elements, h, elements, h + 1, mask - h);
            }
            elements[h] = null;
            head = (h + 1) & mask;
            return false;
        } else {
            if (i < t) { // Copy the null tail as well 这样复制少点
                System.arraycopy(elements, i + 1, elements, i, back);
                tail = t - 1;
            } else { // Wrap around
                System.arraycopy(elements, i + 1, elements, i, mask - i);
                elements[mask] = elements[0];
                System.arraycopy(elements, 1, elements, 0, t);
                tail = (t - 1) & mask;
            }
            return true;
        }
    }
```
移除元素数组中指定位置的元素，根据需要调整头部和尾部。这可能导致数组中的元素向后或向前移动。

这个方法被称为delete而不是remove，以强调它的语义不同于{@link List#remove(int)}。
### Return
如果元素向后移动，则为真







# Collection Methods
## size
```java
    public int size() {
        return (tail - head) & (elements.length - 1);
    }
```
返回此deque中的元素数量。
### Return
这个deque中的元素个数





## isEmpty
```java
    public boolean isEmpty() {
        return head == tail;
    }
```
如果该deque不包含元素，则返回true。
### Return
如果这个deque不包含元素，则为true







## iterator
```java
    public Iterator<E> iterator() {
        return new DeqIterator();
    }

    // 以相反的顺序返回deque中元素的迭代器。元素将按从最后(tail)到第一个(head)的顺序返回。
    public Iterator<E> descendingIterator() {
        return new DescendingIterator();
    }

    private class DeqIterator implements Iterator<E> {
        /**
         * 元素的索引，将由后续调用next返回。
         */
        private int cursor = head;

        /**
         * 尾部记录，停止迭代器，并检查并发。
         */
        private int fence = tail;

        /**
         * 最近一次调用next返回的元素的索引。
         * 如果元素被要删除的调用删除，则重置为-1。
         */
        private int lastRet = -1;

        public boolean hasNext() {
            return cursor != fence;
        }

        public E next() {
            if (cursor == fence)
                throw new NoSuchElementException();
            @SuppressWarnings("unchecked")
            E result = (E) elements[cursor];
            // This check doesn't catch all possible comodifications,
            // but does catch the ones that corrupt traversal
            if (tail != fence || result == null)
                throw new ConcurrentModificationException();
            lastRet = cursor;
            cursor = (cursor + 1) & (elements.length - 1);
            return result;
        }

        public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            if (delete(lastRet)) { // if left-shifted, undo increment in next()
                cursor = (cursor - 1) & (elements.length - 1);
                fence = tail;
            }
            lastRet = -1;
        }

        public void forEachRemaining(Consumer<? super E> action) {
            Objects.requireNonNull(action);
            Object[] a = elements;
            int m = a.length - 1, f = fence, i = cursor;
            cursor = f;
            while (i != f) {
                @SuppressWarnings("unchecked") E e = (E)a[i];
                i = (i + 1) & m;
                if (e == null)
                    throw new ConcurrentModificationException();
                action.accept(e);
            }
        }
    }

    private class DescendingIterator implements Iterator<E> {
        /*
         * This class is nearly a mirror-image of DeqIterator, using
         * tail instead of head for initial cursor, and head instead of
         * tail for fence.
         */
        private int cursor = tail;
        private int fence = head;
        private int lastRet = -1;

        public boolean hasNext() {
            return cursor != fence;
        }

        public E next() {
            if (cursor == fence)
                throw new NoSuchElementException();
            cursor = (cursor - 1) & (elements.length - 1);
            @SuppressWarnings("unchecked")
            E result = (E) elements[cursor];
            if (head != fence || result == null)
                throw new ConcurrentModificationException();
            lastRet = cursor;
            return result;
        }

        public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            if (!delete(lastRet)) {
                cursor = (cursor + 1) & (elements.length - 1);
                fence = head;
            }
            lastRet = -1;
        }
    }
```
返回该deque中元素的迭代器。元素将从第一个(head)到最后一个(tail)排序。这与元素将被排队(通过连续调用remove()或pop(通过连续调用pop())的顺序相同。
### Return
对deque中的元素进行迭代








## contains
```java
    public boolean contains(Object o) {
        if (o == null)
            return false;
        int mask = elements.length - 1;
        int i = head;
        Object x;
        while ( (x = elements[i]) != null) {
            if (o.equals(x))
                return true;
            i = (i + 1) & mask;
        }
        return false;
    }
```
如果此deque包含指定的元素，则返回true。更正式地说，当且仅当这个deque包含至少一个元素e，使得o.equals(e)时返回true。
### Param
o - 要检查此deque中的容器的对象
### Return
如果这个deque包含指定的元素，则为true





## remove
```java
    public boolean remove(Object o) {
        return removeFirstOccurrence(o);
    }
```
从该deque中删除指定元素的单个实例。如果deque不包含该元素，它将保持不变。更正式地说，删除第一个元素e，使o.equals(e)(如果存在这样一个元素)。如果这个deque包含指定的元素，则返回true(如果这个deque由于调用而更改，则返回true)。
### Param
o - 如果存在，则从该deque中删除元素
### Return
如果这个deque包含指定的元素，则为true



## clear
```java
    public void clear() {
        int h = head;
        int t = tail;
        if (h != t) { // clear all cells
            head = tail = 0;
            int i = h;
            int mask = elements.length - 1;
            do {
                elements[i] = null;
                i = (i + 1) & mask;
            } while (i != t);
        }
    }
```
从deque中删除所有元素。此调用返回后，deque将为空。






## toArray
```java
    public Object[] toArray() {
        return copyElements(new Object[size()]);
    }
```
返回一个数组，该数组按正确的顺序(从第一个元素到最后一个元素)包含deque中的所有元素。

返回的数组将是“安全的”，因为这个deque不维护对它的引用。(换句话说，这个方法必须分配一个新的数组)。因此，调用者可以自由地修改返回的数组。

此方法充当基于数组和基于集合的api之间的桥梁。
### Return
包含deque中所有元素的数组




## toArray
```java
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        int size = size();
        if (a.length < size)
            a = (T[])java.lang.reflect.Array.newInstance(
                    a.getClass().getComponentType(), size);
        copyElements(a);
        if (a.length > size)
            a[size] = null;
        return a;
    }
```
返回一个数组，该数组按正确的顺序包含deque中的所有元素(从第一个元素到最后一个元素);返回数组的运行时类型是指定数组的运行时类型。如果deque符合指定的数组，则返回deque。否则，将使用指定数组的运行时类型和该deque的大小分配一个新数组。

如果这个deque符合指定的数组，则有剩余空间(即，数组中元素个数多于deque)，紧接deque结尾的数组中的元素被设置为null。

与toArray()方法一样，该方法充当基于数组和基于集合的api之间的桥梁。此外，这种方法允许对输出数组的运行时类型进行精确控制，在某些情况下，还可以用来节省分配成本。
### Type Parameters:
T - 包含集合的数组的运行时类型
### Param
a - 如果deque的元素足够大，则将其存储到其中的数组;否则，将为此目的分配相同运行时类型的新数组
### Return
包含deque中所有元素的数组






# Object methods
## clone
```java
    public ArrayDeque<E> clone() {
        try {
            @SuppressWarnings("unchecked")
            ArrayDeque<E> result = (ArrayDeque<E>) super.clone();
            result.elements = Arrays.copyOf(elements, elements.length);
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }

    private static final long serialVersionUID = 2340985798034038923L;
```
返回此deque的副本。






## writeObject
```java
    private void writeObject(java.io.ObjectOutputStream s)
            throws java.io.IOException {
        s.defaultWriteObject();

        // Write out size
        s.writeInt(size());

        // Write out elements in order.
        int mask = elements.length - 1;
        for (int i = head; i != tail; i = (i + 1) & mask)
            s.writeObject(elements[i]);
    }
```








## readObject
```java
    private void readObject(java.io.ObjectInputStream s)
            throws java.io.IOException, ClassNotFoundException {
        s.defaultReadObject();

        // Read in size and allocate array
        int size = s.readInt();
        int capacity = calculateSize(size);
        SharedSecrets.getJavaOISAccess().checkArray(s, Object[].class, capacity);
        allocateElements(size);
        head = 0;
        tail = size;

        // Read in all elements in the proper order.
        for (int i = 0; i < size; i++)
            elements[i] = s.readObject();
    }
```









## spliterator
```java
    public Spliterator<E> spliterator() {
        return new DeqSpliterator<E>(this, -1, -1);
    }

    static final class DeqSpliterator<E> implements Spliterator<E> {
        private final ArrayDeque<E> deq;
        private int fence;  // -1 until first use
        private int index;  // current index, modified on traverse/split

        /** Creates new spliterator covering the given array and range */
        DeqSpliterator(ArrayDeque<E> deq, int origin, int fence) {
            this.deq = deq;
            this.index = origin;
            this.fence = fence;
        }

        private int getFence() { // force initialization
            int t;
            if ((t = fence) < 0) {
                t = fence = deq.tail;
                index = deq.head;
            }
            return t;
        }

        public DeqSpliterator<E> trySplit() {
            int t = getFence(), h = index, n = deq.elements.length;
            if (h != t && ((h + 1) & (n - 1)) != t) {
                if (h > t)
                    t += n;
                int m = ((h + t) >>> 1) & (n - 1);
                return new DeqSpliterator<>(deq, h, index = m);
            }
            return null;
        }

        public void forEachRemaining(Consumer<? super E> consumer) {
            if (consumer == null)
                throw new NullPointerException();
            Object[] a = deq.elements;
            int m = a.length - 1, f = getFence(), i = index;
            index = f;
            while (i != f) {
                @SuppressWarnings("unchecked") E e = (E)a[i];
                i = (i + 1) & m;
                if (e == null)
                    throw new ConcurrentModificationException();
                consumer.accept(e);
            }
        }

        public boolean tryAdvance(Consumer<? super E> consumer) {
            if (consumer == null)
                throw new NullPointerException();
            Object[] a = deq.elements;
            int m = a.length - 1, f = getFence(), i = index;
            if (i != fence) {
                @SuppressWarnings("unchecked") E e = (E)a[i];
                index = (i + 1) & m;
                if (e == null)
                    throw new ConcurrentModificationException();
                consumer.accept(e);
                return true;
            }
            return false;
        }

        public long estimateSize() {
            int n = getFence() - index;
            if (n < 0)
                n += deq.elements.length;
            return (long) n;
        }

        @Override
        public int characteristics() {
            return Spliterator.ORDERED | Spliterator.SIZED |
                Spliterator.NONNULL | Spliterator.SUBSIZED;
        }
    }
```
在deque中的元素上创建一个延迟绑定和故障快速Spliterator。
Spliterator报告Spliterator。大小,Spliterator。小尺寸,Spliterator。命令,Spliterator.NONNULL。覆盖实现应该记录额外特征值的报告。