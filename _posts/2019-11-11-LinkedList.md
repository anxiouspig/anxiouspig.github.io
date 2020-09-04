---
layout:     post
title:      LinkedList
subtitle:   LinkedList
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
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```
双链表实现了列表和Deque接口。实现所有可选列表操作，并允许所有元素(包括null)。

所有操作的执行都符合双链表的预期。索引到列表中的操作将从头到尾遍历列表，以更接近指定索引的操作为准。

注意，这个实现不是同步的。如果多个线程同时访问一个链表，并且其中至少有一个线程从结构上修改链表，那么它必须在外部同步。(结构修改是添加或删除一个或多个元素的任何操作;仅仅设置元素的值并不是结构上的修改。)这通常是通过对一些自然封装列表的对象进行同步来实现的。如果不存在这样的对象，则应该使用集合“包装”列表。synchronizedList方法。这最好在创建时完成，以防止意外的不同步访问列表:
```java
   List list = Collections.synchronizedList(new LinkedList(...));
```
这个类的iterator和listIterator方法返回的迭代器是快速故障的:如果在创建迭代器之后的任何时候对列表进行结构修改，除了通过迭代器自己的remove或add方法，迭代器将抛出ConcurrentModificationException。因此，在面对并发修改时，迭代器会快速而干净地失败，而不是在将来某个不确定的时间冒着任意的、不确定的行为的风险。

注意，不能保证迭代器的快速故障行为，因为通常来说，在存在非同步并发修改的情况下，不可能做出任何严格的保证。故障快速迭代器以最大的努力抛出ConcurrentModificationException。因此，编写一个依赖于此异常来判断其正确性的程序是错误的:迭代器的快速故障行为应该只用于检测bug。

该类是Java集合框架的成员。
### Type Param
E - 此集合中包含的元素的类型
### Since 1.2






# Attribute
```java
    transient int size = 0;
```
元素数量


```java
    transient Node<E> first;
```
指向第一个节点的指针。



```java
    transient Node<E> last;
```
指向最后一个节点的指针。





# Constuctor
```java
    public LinkedList() {
    }
```
构造一个空列表。


```java
    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }
```
构造包含指定集合的元素的列表，按集合的迭代器返回元素的顺序排列。
### Param
c - 要将其元素放入此列表的集合





# Method
## linkFirst
```java
    private void linkFirst(E e) {
        final Node<E> f = first;
        final Node<E> newNode = new Node<>(null, e, f);
        first = newNode;
        if (f == null)
            last = newNode;
        else
            f.prev = newNode;
        size++;
        modCount++;
    }
```
链接e作为第一个元素。








## linkLast
```java
    void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
```
链接e作为最后一个元素。






## linkBefore
```java
    void linkBefore(E e, Node<E> succ) {
        // assert succ != null;
        final Node<E> pred = succ.prev;
        final Node<E> newNode = new Node<>(pred, e, succ);
        succ.prev = newNode;
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        size++;
        modCount++;
    }
```
在非空节点succ之前插入元素e。








## unlinkFirst
```java
    private E unlinkFirst(Node<E> f) {
        // assert f == first && f != null;
        final E element = f.item;
        final Node<E> next = f.next;
        f.item = null;
        f.next = null; // help GC
        first = next;
        if (next == null)
            last = null;
        else
            next.prev = null;
        size--;
        modCount++;
        return element;
    }
```
断开非空第一个节点f的链接。









## unlinkLast
```java
    private E unlinkLast(Node<E> l) {
        // assert l == last && l != null;
        final E element = l.item;
        final Node<E> prev = l.prev;
        l.item = null;
        l.prev = null; // help GC
        last = prev;
        if (prev == null)
            first = null;
        else
            prev.next = null;
        size--;
        modCount++;
        return element;
    }
```
断开非空的最后一个节点l。







## unlink
```java
    E unlink(Node<E> x) {
        // assert x != null;
        final E element = x.item;
        final Node<E> next = x.next;
        final Node<E> prev = x.prev;

        if (prev == null) {
            first = next;
        } else {
            prev.next = next;
            x.prev = null;
        }

        if (next == null) {
            last = prev;
        } else {
            next.prev = prev;
            x.next = null;
        }

        x.item = null;
        size--;
        modCount++;
        return element;
    }
```
断开非空节点x的链接。








## getFirst
```java
    public E getFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return f.item;
    }
```
返回列表中的第一个元素。
### Return
列表中的第一个元素







## getLast
```java
    public E getLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return l.item;
    }
```
返回列表中的最后一个元素。
### Return
列表中的最后一个元素









## removeFirst
```java
    public E removeFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return unlinkFirst(f);
    }
```
从列表中移除并返回第一个元素。
### Return
列表中的第一个元素







## removeLast
```java
    public E removeLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return unlinkLast(l);
    }
```
从列表中移除并返回最后一个元素。









## addFirst
```java
    public void addFirst(E e) {
        linkFirst(e);
    }
```
在列表的开头插入指定的元素。
### Param
e - 要添加的元素






## addLast
```java
    public void addLast(E e) {
        linkLast(e);
    }
```
将指定的元素追加到此列表的末尾。

This method is equivalent to add(E).
### Param
e - 要添加的元素








## contains
```java
    public boolean contains(Object o) {
        return indexOf(o) != -1;
    }
```
如果此列表包含指定的元素，则返回true。更正式地说，当且仅当此列表包含至少一个元素e (o==null ?e = = null: o.equals (e))。
### Param
o - 元素，其在此列表中的存在性将被测试
### Return
如果此列表包含指定的元素，则为true






## size
```java
    public int size() {
        return size;
    }
```
返回此列表中的元素数量。
### Return
列表中元素的数量








## add
```java
    public boolean add(E e) {
        linkLast(e);
        return true;
    }
```
将指定的元素追加到此列表的末尾。

这个方法等价于addLast(E)。
### Param
e - 要附加到此列表中的元素
### Return
true(由Collection.add(E)指定)






## remove
```java
    public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
```
从列表中删除指定元素的第一个出现项(如果存在)。如果这个列表不包含这个元素，它将保持不变。更正式地说，删除索引i最低的元素，使(o==null ?get(i)==null: o.quals(get(i))(如果存在这样一个元素)。如果此列表包含指定的元素，则返回true(如果此列表由于调用而更改，则返回true)。
### Param
o - 元素，如果存在，则从该列表中删除
### Return
如果此列表包含指定的元素，则为true






## addAll
```java
    public boolean addAll(Collection<? extends E> c) {
        return addAll(size, c);
    }
```
按指定集合的迭代器返回的顺序，将指定集合中的所有元素追加到此列表的末尾。如果在操作进行期间修改了指定的集合，则此操作的行为未定义。(注意，如果指定的集合是这个列表，并且它不是空的，则会发生这种情况。)
### Param
c - 集合，其中包含要添加到此列表中的元素
### Return
如果此列表因调用而更改，则为true





## addAll
```java
    public boolean addAll(int index, Collection<? extends E> c) {
        checkPositionIndex(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        if (numNew == 0)
            return false;

        Node<E> pred, succ;
        if (index == size) {
            succ = null;
            pred = last;
        } else {
            succ = node(index);
            pred = succ.prev;
        }

        for (Object o : a) {
            @SuppressWarnings("unchecked") E e = (E) o;
            Node<E> newNode = new Node<>(pred, e, null);
            if (pred == null)
                first = newNode;
            else
                pred.next = newNode;
            pred = newNode;
        }

        if (succ == null) {
            last = pred;
        } else {
            pred.next = succ;
            succ.prev = pred;
        }

        size += numNew;
        modCount++;
        return true;
    }
```
从指定位置开始，将指定集合中的所有元素插入此列表。将当前位于该位置的元素(如果有的话)和随后的任何元素移动到右边(增加它们的索引)。新元素将按指定集合的迭代器返回它们的顺序出现在列表中。
### Param
index - 从指定集合插入第一个元素的索引
c - 包含要添加到此列表中的元素的集合
### Return
如果此列表因调用而更改，则为true










## clear
```java
    public void clear() {
        // Clearing all of the links between nodes is "unnecessary", but:
        // - helps a generational GC if the discarded nodes inhabit
        //   more than one generation
        // - is sure to free memory even if there is a reachable Iterator
        for (Node<E> x = first; x != null; ) {
            Node<E> next = x.next;
            x.item = null;
            x.next = null;
            x.prev = null;
            x = next;
        }
        first = last = null;
        size = 0;
        modCount++;
    }
```
从列表中删除所有元素。该调用返回后，列表将为空。









## get
```java
    public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
```
返回列表中指定位置的元素。
### Param
index - 要返回的元素的索引
### Return
位于列表中指定位置的元素





## set
```java
    public E set(int index, E element) {
        checkElementIndex(index);
        Node<E> x = node(index);
        E oldVal = x.item;
        x.item = element;
        return oldVal;
    }
```
用指定的元素替换列表中指定位置的元素。
### Param
index - 要返回的元素的索引
element - 要存储在指定位置的元素
### Return
先前位于指定位置的元素





## add
```java
    public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
```
将指定元素插入到列表中的指定位置。将当前位于该位置的元素(如果有的话)和随后的任何元素向右移动(将一个元素添加到它们的索引中)。
### Param
index - 要插入指定元素的索引
element - 要插入的元素








## remove
```java
    public E remove(int index) {
        checkElementIndex(index);
        return unlink(node(index));
    }
```
移除列表中指定位置的元素。将任何后续元素向左移动(从它们的索引中减去一个)。返回从列表中删除的元素。
### Param
index - 要删除的元素的索引
### Return
先前位于指定位置的元素








## isElementIndex
```java
    private boolean isElementIndex(int index) {
        return index >= 0 && index < size;
    }
```
说明参数是否为现有元素的索引。







## isPositionIndex
```java
    private boolean isPositionIndex(int index) {
        return index >= 0 && index <= size;
    }
```
说明参数是迭代器或add操作的有效位置的索引。







## outOfBoundsMsg
```java
    private String outOfBoundsMsg(int index) {
        return "Index: "+index+", Size: "+size;
    }
```
构造IndexOutOfBoundsException详细信息。







## checkElementIndex
```java
    private void checkElementIndex(int index) {
        if (!isElementIndex(index))
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```





## checkPositionIndex
```java
    private void checkPositionIndex(int index) {
        if (!isPositionIndex(index))
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```







## node
```java
    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
```
返回指定元素索引处的(非空)节点。







## indexOf
```java
    public int indexOf(Object o) {
        int index = 0;
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null)
                    return index;
                index++;
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item))
                    return index;
                index++;
            }
        }
        return -1;
    }
```
返回此列表中指定元素的第一个出现项的索引，如果该列表不包含该元素，则返回-1。更正式地说，返回最低索引，如果没有这样的索引，则返回-1。
### Param
o - 要搜索的元素
### Return
此列表中指定元素第一次出现的索引，如果该列表不包含该元素，则为-1







## lastIndexOf
```java
    public int lastIndexOf(Object o) {
        int index = size;
        if (o == null) {
            for (Node<E> x = last; x != null; x = x.prev) {
                index--;
                if (x.item == null)
                    return index;
            }
        } else {
            for (Node<E> x = last; x != null; x = x.prev) {
                index--;
                if (o.equals(x.item))
                    return index;
            }
        }
        return -1;
    }
```
返回此列表中指定元素的最后一次出现的索引，如果该列表不包含该元素，则返回-1。
### Param
o - 要搜索的元素
### Return
此列表中指定元素的最后一次出现的索引，如果该列表不包含该元素，则为-1






# Queue operations method
## peek
```java
    public E peek() {
        final Node<E> f = first;
        return (f == null) ? null : f.item;
    }
```
检索但不删除此列表的头(第一个元素)。
### Return
列表的头部，如果列表为空，则为空






## element
```java
    public E element() {
        return getFirst();
    }
```
检索但不删除此列表的头(第一个元素)。
### Return
这个列表的头






## poll
```java
    public E poll() {
        final Node<E> f = first;
        return (f == null) ? null : unlinkFirst(f);
    }
```
检索并删除此列表的头部(第一个元素)。
### Return
列表的头部，如果列表为空，则为空






## remove
```java
    public E remove() {
        return removeFirst();
    }
```
检索并删除此列表的头部(第一个元素)。
### Return
列表的头部





## offer
```java
    public boolean offer(E e) {
        return add(e);
    }
```
将指定的元素添加为列表的尾部(最后一个元素)。
### Param
e - 要添加的元素
### Return
true(由Queue.offer(E)指定)








## offerFirst
```java
    public boolean offerFirst(E e) {
        addFirst(e);
        return true;
    }
```
将指定的元素插入到此列表的前面。
### Param
e - 要添加的元素
### Return
true (as specified by Deque.offerLast(E))






## offerLast
```java
    public boolean offerLast(E e) {
        addLast(e);
        return true;
    }
```
在列表末尾插入指定的元素。
### Param
e - 要添加的元素
### Return
true (as specified by Deque.offerLast(E))







## peekFirst
```java
    public E peekFirst() {
        final Node<E> f = first;
        return (f == null) ? null : f.item;
     }
```
检索但不删除此列表的第一个元素，如果该列表为空，则返回null。
### Return
此列表的第一个元素，如果该列表为空，则为空






## peekLast
```java
    public E peekLast() {
        final Node<E> l = last;
        return (l == null) ? null : l.item;
    }
```
检索但不删除此列表的最后一个元素，如果该列表为空，则返回null。
### Return
此列表的最后一个元素，如果该列表为空，则为空









## pollFirst
```java
    public E pollFirst() {
        final Node<E> f = first;
        return (f == null) ? null : unlinkFirst(f);
    }
```
检索并删除此列表的第一个元素，如果该列表为空，则返回null。
### Return
此列表的第一个元素，如果该列表为空，则为空






## pollLast
```java
    public E pollLast() {
        final Node<E> l = last;
        return (l == null) ? null : unlinkLast(l);
    }
```
检索并删除此列表的最后一个元素，如果该列表为空，则返回null。
### Return
此列表的最后一个元素，如果该列表为空，则为空







## push
```java
    public void push(E e) {
        addFirst(e);
    }
```
将元素推入此列表所表示的堆栈。换句话说，将元素插入到列表的前面。

This method is equivalent to addFirst(E).
### Param
e - 要推的元素







## pop
```java
    public E pop() {
        return removeFirst();
    }
```
从这个列表表示的堆栈中弹出一个元素。换句话说，删除并返回列表的第一个元素。

This method is equivalent to removeFirst().
### Return
这个列表前面的元素(它是由这个列表表示的堆栈的顶部)








## removeFirstOccurrence
```java
    public boolean removeFirstOccurrence(Object o) {
        return remove(o);
    }
```
删除此列表中指定元素的第一个出现项(当从头到尾遍历列表时)。如果列表不包含该元素，它将保持不变。
### Param
o - 元素，如果存在，则从该列表中删除
### Return
如果列表包含指定的元素，则为true







## removeLastOccurrence
```java
    public boolean removeLastOccurrence(Object o) {
        if (o == null) {
            for (Node<E> x = last; x != null; x = x.prev) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = last; x != null; x = x.prev) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
```
删除此列表中指定元素的最后一次出现(当从头到尾遍历列表时)。如果列表不包含该元素，它将保持不变。
### Param
o - 元素，如果存在，则从该列表中删除
### Return
如果列表包含指定的元素，则为true






## listIterator
```java
    public ListIterator<E> listIterator(int index) {
        checkPositionIndex(index);
        return new ListItr(index);
    }

    private class ListItr implements ListIterator<E> {
        private Node<E> lastReturned;
        private Node<E> next;
        private int nextIndex;
        private int expectedModCount = modCount;

        ListItr(int index) {
            // assert isPositionIndex(index);
            next = (index == size) ? null : node(index);
            nextIndex = index;
        }

        public boolean hasNext() {
            return nextIndex < size;
        }

        public E next() {
            checkForComodification();
            if (!hasNext())
                throw new NoSuchElementException();

            lastReturned = next;
            next = next.next;
            nextIndex++;
            return lastReturned.item;
        }

        public boolean hasPrevious() {
            return nextIndex > 0;
        }

        public E previous() {
            checkForComodification();
            if (!hasPrevious())
                throw new NoSuchElementException();

            lastReturned = next = (next == null) ? last : next.prev;
            nextIndex--;
            return lastReturned.item;
        }

        public int nextIndex() {
            return nextIndex;
        }

        public int previousIndex() {
            return nextIndex - 1;
        }

        public void remove() {
            checkForComodification();
            if (lastReturned == null)
                throw new IllegalStateException();

            Node<E> lastNext = lastReturned.next;
            unlink(lastReturned);
            if (next == lastReturned)
                next = lastNext;
            else
                nextIndex--;
            lastReturned = null;
            expectedModCount++;
        }

        public void set(E e) {
            if (lastReturned == null)
                throw new IllegalStateException();
            checkForComodification();
            lastReturned.item = e;
        }

        public void add(E e) {
            checkForComodification();
            lastReturned = null;
            if (next == null)
                linkLast(e);
            else
                linkBefore(e, next);
            nextIndex++;
            expectedModCount++;
        }

        public void forEachRemaining(Consumer<? super E> action) {
            Objects.requireNonNull(action);
            while (modCount == expectedModCount && nextIndex < size) {
                action.accept(next.item);
                lastReturned = next;
                next = next.next;
                nextIndex++;
            }
            checkForComodification();
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }

    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```
返回此列表中元素的列表迭代器(按适当的顺序)，从列表中的指定位置开始。遵守List.listIterator(int)的总合同。

列表迭代器是快速故障的:如果在创建列表迭代器之后，列表在结构上随时被修改，除了通过列表迭代器自己的删除或添加方法外，列表迭代器将抛出ConcurrentModificationException。因此，在面对并发修改时，迭代器会快速而干净地失败，而不是在将来某个不确定的时间冒着任意的、不确定的行为的风险。
### Param
index - 列表迭代器返回的第一个元素的索引(通过调用next)
### Return
列表中元素的ListIterator(按适当的顺序)，从列表中的指定位置开始









## descendingIterator
```java
    public Iterator<E> descendingIterator() {
        return new DescendingIterator();
    }
```
以相反的顺序返回deque中元素的迭代器。元素将按从最后(tail)到第一个(head)的顺序返回。





## DescendingIterator
```java
    private class DescendingIterator implements Iterator<E> {
        private final ListItr itr = new ListItr(size());
        public boolean hasNext() {
            return itr.hasPrevious();
        }
        public E next() {
            return itr.previous();
        }
        public void remove() {
            itr.remove();
        }
    }

    @SuppressWarnings("unchecked")
    private LinkedList<E> superClone() {
        try {
            return (LinkedList<E>) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new InternalError(e);
        }
    }
```






## clone
```java
    public Object clone() {
        LinkedList<E> clone = superClone();

        // Put clone into "virgin" state
        clone.first = clone.last = null;
        clone.size = 0;
        clone.modCount = 0;

        // Initialize clone with our elements
        for (Node<E> x = first; x != null; x = x.next)
            clone.add(x.item);

        return clone;
    }
```
返回这个LinkedList的浅拷贝。(元素本身不是克隆的。)





## toArray
```java
    public Object[] toArray() {
        Object[] result = new Object[size];
        int i = 0;
        for (Node<E> x = first; x != null; x = x.next)
            result[i++] = x.item;
        return result;
    }
```
返回一个数组，该数组按适当的顺序(从第一个元素到最后一个元素)包含列表中的所有元素。

返回的数组将是“安全的”，因为这个列表不维护对它的引用。(换句话说，这个方法必须分配一个新的数组)。因此，调用者可以自由地修改返回的数组。

此方法充当基于数组和基于集合的api之间的桥梁。




## toArray
```java
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        if (a.length < size)
            a = (T[])java.lang.reflect.Array.newInstance(
                                a.getClass().getComponentType(), size);
        int i = 0;
        Object[] result = a;
        for (Node<E> x = first; x != null; x = x.next)
            result[i++] = x.item;

        if (a.length > size)
            a[size] = null;

        return a;
    }
```
返回一个数组，该数组包含列表中按正确顺序排列的所有元素(从第一个元素到最后一个元素);返回数组的运行时类型是指定数组的运行时类型。如果列表符合指定的数组，则返回其中的列表。否则，将使用指定数组的运行时类型和该列表的大小分配一个新数组。




## writeObject
```java
    private static final long serialVersionUID = 876323262645176354L;
    
        private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException {
        // Write out any hidden serialization magic
        s.defaultWriteObject();

        // Write out size
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (Node<E> x = first; x != null; x = x.next)
            s.writeObject(x.item);
    }
```




## readObject
```java
    @SuppressWarnings("unchecked")
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        // Read in any hidden serialization magic
        s.defaultReadObject();

        // Read in size
        int size = s.readInt();

        // Read in all elements in the proper order.
        for (int i = 0; i < size; i++)
            linkLast((E)s.readObject());
    }
```








## spliterator
```java
    @Override
    public Spliterator<E> spliterator() {
        return new LLSpliterator<E>(this, -1, 0);
    }

    /** A customized variant of Spliterators.IteratorSpliterator */
    static final class LLSpliterator<E> implements Spliterator<E> {
        static final int BATCH_UNIT = 1 << 10;  // batch array size increment
        static final int MAX_BATCH = 1 << 25;  // max batch array size;
        final LinkedList<E> list; // null OK unless traversed
        Node<E> current;      // current node; null until initialized
        int est;              // size estimate; -1 until first needed
        int expectedModCount; // initialized when est set
        int batch;            // batch size for splits

        LLSpliterator(LinkedList<E> list, int est, int expectedModCount) {
            this.list = list;
            this.est = est;
            this.expectedModCount = expectedModCount;
        }

        final int getEst() {
            int s; // force initialization
            final LinkedList<E> lst;
            if ((s = est) < 0) {
                if ((lst = list) == null)
                    s = est = 0;
                else {
                    expectedModCount = lst.modCount;
                    current = lst.first;
                    s = est = lst.size;
                }
            }
            return s;
        }

        public long estimateSize() { return (long) getEst(); }

        public Spliterator<E> trySplit() {
            Node<E> p;
            int s = getEst();
            if (s > 1 && (p = current) != null) {
                int n = batch + BATCH_UNIT;
                if (n > s)
                    n = s;
                if (n > MAX_BATCH)
                    n = MAX_BATCH;
                Object[] a = new Object[n];
                int j = 0;
                do { a[j++] = p.item; } while ((p = p.next) != null && j < n);
                current = p;
                batch = j;
                est = s - j;
                return Spliterators.spliterator(a, 0, j, Spliterator.ORDERED);
            }
            return null;
        }

        public void forEachRemaining(Consumer<? super E> action) {
            Node<E> p; int n;
            if (action == null) throw new NullPointerException();
            if ((n = getEst()) > 0 && (p = current) != null) {
                current = null;
                est = 0;
                do {
                    E e = p.item;
                    p = p.next;
                    action.accept(e);
                } while (p != null && --n > 0);
            }
            if (list.modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }

        public boolean tryAdvance(Consumer<? super E> action) {
            Node<E> p;
            if (action == null) throw new NullPointerException();
            if (getEst() > 0 && (p = current) != null) {
                --est;
                E e = p.item;
                current = p.next;
                action.accept(e);
                if (list.modCount != expectedModCount)
                    throw new ConcurrentModificationException();
                return true;
            }
            return false;
        }

        public int characteristics() {
            return Spliterator.ORDERED | Spliterator.SIZED | Spliterator.SUBSIZED;
        }
    }

```
在此列表中的元素上创建延迟绑定和故障快速Spliterator。Spliterator报告Spliterator。大小和Spliterator.ORDERED。覆盖实现应该记录额外特征值的报告。