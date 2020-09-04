---
layout:     post
title:      ArrayList
subtitle:   ArrayList
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
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```
列表接口的可调整数组实现。实现所有可选列表操作，并允许所有元素，包括null。除了实现List接口之外，该类还提供了一些方法来操作用于存储列表的内部数组的大小。(这个类大致相当于Vector，只是它是不同步的。)

size、isEmpty、get、set、iterator和listIterator操作在常量时间内运行。add操作在平摊常数时间内运行，也就是说，添加n个元素需要O(n)时间。所有其他操作都在线性时间内运行(粗略地说)。与LinkedList实现相比，常量因素要低。

每个ArrayList实例都有一个容量。容量是用于存储列表中元素的数组的大小。它总是至少和列表大小一样大。当元素被添加到ArrayList中时，它的容量会自动增长。除了添加一个元素具有恒定的平摊时间代价之外，没有指定增长策略的细节。

在使用ensureCapacity操作添加大量元素之前，应用程序可以增加ArrayList实例的容量。这可能会减少增量重新分配的数量。

注意，这个实现不是同步的。如果多个线程同时访问一个ArrayList实例，并且至少有一个线程从结构上修改了这个列表，那么它必须在外部同步。(结构修改是添加或删除一个或多个元素，或显式调整支持数组的大小的任何操作;仅仅设置元素的值并不是结构上的修改。)这通常是通过对一些自然封装列表的对象进行同步来实现的。如果不存在这样的对象，则应该使用Collections.synchronizedList方法。这最好在创建时完成，以防止意外的不同步访问列表:
```java
   List list = Collections.synchronizedList(new ArrayList(...));
```
这个类的iterator和listIterator方法返回的迭代器是快速故障的:如果在创建迭代器之后的任何时候对列表进行结构修改，除了通过迭代器自己的remove或add方法，迭代器将抛出ConcurrentModificationException。因此，在面对并发修改时，迭代器会快速而干净地失败，而不是在将来某个不确定的时间冒着任意的、不确定的行为的风险。

注意，不能保证迭代器的快速故障行为，因为通常来说，在存在非同步并发修改的情况下，不可能做出任何严格的保证。故障快速迭代器以最大的努力抛出ConcurrentModificationException。因此，编写一个依赖于此异常来判断其正确性的程序是错误的:迭代器的快速故障行为应该只用于检测bug。

该类是Java集合框架的成员。
### All Implemented Interfaces:
Serializable, Cloneable, Iterable<E>, Collection<E>, List<E>, RandomAccess
### Direct Known Subclasses:
AttributeList, RoleList, RoleUnresolvedList
### Since 1.2
### See Also:
Collection, List, LinkedList, Vector, Serialized Form

















# Attribute
```java
    private static final long serialVersionUID = 8683452581122892189L;
```
序列化id




```java
private static final int DEFAULT_CAPACITY = 10;
```
默认容量




```java
private static final Object[] EMPTY_ELEMENTDATA = {};
```
空数组





```java
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
```
默认容量空数组





```java
transient Object[] elementData; // non-private to simplify nested class access
```
非私有的，方便内部类访问






```java
private int size;
```
数组大小










# Constructor
```java
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
```
构造具有指定初始容量的空列表。
### Parameters:
initialCapacity - 列表的初始容量
### Throws:
IllegalArgumentException - 如果指定的初始容量为负




```java
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```
构造一个初始容量为10的空列表。




```java
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            // c.toArray 可能 (incorrectly) 不返回 Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // 替换为空数组
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```
构造包含指定集合的元素的列表，按集合的迭代器返回元素的顺序排列。
### Parameters:
c - 其元素将被放入此列表的集合
### Throws:
NullPointerException - 如果指定的集合为空







# Method
## trimToSize
```java
    public void trimToSize() {
        modCount++;
        if (size < elementData.length) {
            elementData = (size == 0)
              ? EMPTY_ELEMENTDATA
              : Arrays.copyOf(elementData, size);
        }
    }
```
将ArrayList实例的容量调整为列表的当前大小。应用程序可以使用此操作最小化ArrayList实例的存储。









## ensureCapacity
```java
    public void ensureCapacity(int minCapacity) {
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }
```
如果需要，增加这个ArrayList实例的容量，以确保它至少可以容纳由最小容量参数指定的元素数量。
### Parameters:
minCapacity - 所需的最小容量




## calculateCapacity
```java
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
```
计算容量




## ensureCapacityInternal
```java
    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
```


## ensureExplicitCapacity
```java
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
```



## grow
```java
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```
要分配的数组的最大大小。
一些vm在数组中保留一些头信息。
试图分配更大的数组可能会导致OutOfMemoryError:请求的数组大小超过VM限制




```java
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
增加容量，以确保它至少可以容纳由最小容量参数指定的元素数量。
### Parameters:
minCapacity - 所需的最小容量





```java
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```







## size
```java
    public int size() {
        return size;
    }
```
返回此列表中的元素数量。
### Specified by:
size in interface Collection<E>
### Specified by:
size in class AbstractCollection<E>
### Returns:
列表中元素的数量




## isEmpty
```java
    public boolean isEmpty() {
        return size == 0;
    }
```
如果此列表不包含任何元素，则返回true。
### Specified by:
isEmpty in interface Collection<E>
### Specified by:
isEmpty in interface List<E>
### Overrides:
isEmpty in class AbstractCollection<E>
### Returns:
如果此列表不包含元素，则为true





## contains
```java
    public boolean contains(Object o) {
        return indexOf(o) >= 0;
    }
```
如果此列表包含指定的元素，则返回true。更正式地说，当且仅当此列表包含至少一个元素e,(o==null ? e==null : o.equals(e))。
### Specified by:
contains in interface Collection<E>
### Specified by:
contains in interface List<E>
### Overrides:
contains in class AbstractCollection<E>
### Parameters:
o - 元素，其在此列表中的存在性将被测试
### Returns:
如果此列表包含指定的元素，则为true





## indexOf
```java
    public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }
```
返回此列表中指定元素的第一个出现项的索引，如果该列表不包含该元素，则返回-1。更正式地说，返回最低索引i，使(o==null ?get(i)==null : o.equals(get(i))，如果没有这样的索引，则为-1。
### Specified by:
indexOf in interface List<E>
### Overrides:
indexOf in class AbstractList<E>
### Parameters:
o - 要搜索的元素
### Returns:
此列表中指定元素第一次出现的索引，如果该列表不包含该元素，则为-1




## lastIndexOf
```java
    public int lastIndexOf(Object o) {
        if (o == null) {
            for (int i = size-1; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = size-1; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }
```
返回此列表中指定元素的最后一次出现的索引，如果该列表不包含该元素，则返回-1。更正式地说，返回最高索引i，使(o==null ?get(i)==null: o.equals(get(i))，如果没有这样的索引，则为-1。
### Specified by:
lastIndexOf in interface List<E>
### Overrides:
lastIndexOf in class AbstractList<E>
### Parameters:
o - 要搜索的元素
### Returns:
此列表中指定元素的最后一次出现的索引，如果该列表不包含该元素，则为-1





## clone
```java
    public Object clone() {
        try {
            ArrayList<?> v = (ArrayList<?>) super.clone();
            v.elementData = Arrays.copyOf(elementData, size);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }
```
返回此ArrayList实例的浅副本。(元素本身不会被复制。)
### Overrides:
clone in class Object
### Returns:
这个ArrayList实例的克隆
### See Also:
Cloneable





## toArray
```java
    public Object[] toArray() {
        return Arrays.copyOf(elementData, size);
    }
```
返回一个数组，该数组按适当的顺序(从第一个元素到最后一个元素)包含列表中的所有元素。

返回的数组将是“安全的”，因为这个列表不维护对它的引用。(换句话说，这个方法必须分配一个新的数组)。因此，调用者可以自由地修改返回的数组。

此方法充当基于数组和基于集合的api之间的桥梁。
### Specified by:
toArray in interface Collection<E>
### Specified by:
toArray in interface List<E>
### Overrides:
toArray in class AbstractCollection<E>
### Returns:
一个数组，按适当的顺序包含列表中的所有元素
### See Also:
Arrays.asList(Object[])









## toArray
```java
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        if (a.length < size)
            // Make a new array of a's runtime type, but my contents:
            return (T[]) Arrays.copyOf(elementData, size, a.getClass());
        System.arraycopy(elementData, 0, a, 0, size);
        // 这个判断可能是为了防止某种bug出现吧
        if (a.length > size)
            a[size] = null;
        return a;
    }
```
返回一个数组，该数组包含列表中按正确顺序排列的所有元素(从第一个元素到最后一个元素);返回数组的运行时类型是指定数组的运行时类型。如果列表符合指定的数组，则返回其中的列表。否则，将使用指定数组的运行时类型和该列表的大小分配一个新数组。

如果列表符合指定的数组，则有剩余空间(即，数组中的元素比列表中的元素多)，集合结束后数组中的元素被设置为null。(只有当调用者知道列表不包含任何空元素时，这才有助于确定列表的长度。)
### Specified by:
toArray in interface Collection<E>
### Specified by:
toArray in interface List<E>
### Overrides:
toArray in class AbstractCollection<E>
### Type Parameters:
T - 包含集合的数组的运行时类型
### Parameters:
a - 如果列表的元素足够大，则将其存储到其中的数组;否则，将为此目的分配相同运行时类型的新数组。
### Returns:
包含列表元素的数组
### Throws:
ArrayStoreException - 如果指定数组的运行时类型不是此列表中每个元素的运行时类型的超类型
NullPointerException - 如果指定的数组为空






**Positional Access Operations**
## elementData
```java
    @SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }
```
取出索引位置的元素







## get
```java
    public E get(int index) {
        rangeCheck(index);

        return elementData(index);
    }
```
返回列表中指定位置的元素。
### Specified by:
get in interface List<E>
### Specified by:
get in interface AbstractList<E>
### Parameters:
index - 要返回的元素的索引
### Returns:
位于列表中指定位置的元素
### Throws:
IndexOutOfBoundsException - 如果索引超出范围(索引< 0 ||索引>= size())







## set
```java
    public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
```
用指定的元素替换列表中指定位置的元素。
### Specified by:
set in interface List<E>
### Overrides:
set in class AbstractList<E>
### Parameters:
index - 要替换的元素的索引
element - 要存储在指定位置的元素
### Returns:
先前位于指定位置的元素
### Throws:
IndexOutOfBoundsException - 如果索引超出范围(索引< 0 ||索引>= size())







## add
```java
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
```
将指定的元素追加到此列表的末尾。

如果size+1 < elementData.length，那么数组需要扩充到1.5倍
### Specified by:
add in interface Collection<E>
### Specified by:
add in interface List<E>
### Overrides:
add in class AbstractList<E>
### Parameters:
e - 要附加到此列表中的元素
### Returns:
true (as specified by Collection.add(E))






## add
```java
    public void add(int index, E element) {
        rangeCheckForAdd(index); // 索引检查

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
    }
```
将指定元素插入到列表中的指定位置。将当前位于该位置的元素(如果有的话)和随后的任何元素向右移动(将一个元素添加到它们的索引中)。
### Specified by:
add in interface List<E>
### Overrides:
add in class AbstractList<E>
### Parameters:
index - 要插入指定元素的索引
element - 要插入的元素
### Throws:
IndexOutOfBoundsException - 如果索引超出范围(索引< 0 ||索引>= size())







## remove
```java
    public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
```
移除列表中指定位置的元素。将任何后续元素向左移动(从它们的索引中减去一个)。
### Specified by:
remove in interface List<E>
### Overrides:
remove in class AbstractList<E>
### Parameters:
index - 要删除的元素的索引
### Returns:
从列表中删除的元素
### Throws:
IndexOutOfBoundsException - 如果索引超出范围(索引< 0 ||索引>= size())





## remove
```java
    public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

    /*
     * Private remove method that skips bounds checking and does not
     * return the value removed.
     */
    private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
    }
```
从列表中删除指定元素的第一个出现项(如果存在)。如果列表不包含该元素，它将保持不变。更正式地说，删除索引i最低的元素，使(o==null ? get(i)==null : o.equals(get(i))(如果存在这样一个元素)。如果此列表包含指定的元素，则返回true(如果此列表由于调用而更改，则返回true)。
### Specified by:
remove in interface Collection<E>
### Specified by:
remove in interface List<E>
### Overrides:
remove in class AbstractCollection<E>
### Parameters:
o - 元素，如果存在，则从该列表中删除
### Returns:
如果此列表包含指定的元素，则为true








## clear
```java
    public void clear() {
        modCount++;

        // clear to let GC do its work
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }
```
从列表中删除所有元素。该调用返回后，列表将为空。
### Specified by:
clear in interface Collection<E>
### Specified by:
clear in interface List<E>
### Overrides:
clear in class AbstractList<E>





## addAll
```java
    public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount
        System.arraycopy(a, 0, elementData, size, numNew);
        size += numNew;
        return numNew != 0;
    }
```
按指定集合的迭代器返回的顺序，将指定集合中的所有元素追加到此列表的末尾。如果在操作进行期间修改了指定的集合，则此操作的行为未定义。(这意味着，如果指定的集合是这个列表，并且这个列表不是空的，则此调用的行为是未定义的。)
### Specified by:
addAll in interface Collection<E>
### Specified by:
addAll in interface List<E>
### Overrides:
addAll in class AbstractCollection<E>
### Parameters:
c - 集合，其中包含要添加到此列表中的元素
### Returns:
如果此列表因调用而更改，则为true
### Throws:
NullPointerException - 如果指定的集合为空
### See Also:
AbstractCollection.add(Object)







## addAll
```java
    public boolean addAll(int index, Collection<? extends E> c) {
        rangeCheckForAdd(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount

        int numMoved = size - index;
        if (numMoved > 0)
            System.arraycopy(elementData, index, elementData, index + numNew,
                             numMoved);

        System.arraycopy(a, 0, elementData, index, numNew);
        size += numNew;
        return numNew != 0;
    }
```
从指定位置开始，将指定集合中的所有元素插入此列表。将当前位于该位置的元素(如果有的话)和随后的任何元素移动到右边(增加它们的索引)。新元素将按指定集合的迭代器返回它们的顺序出现在列表中。
### Specified by:
addAll in interface List<E>
### Overrides:
addAll in class AbstractList<E>
### Parameters:
index - 从指定集合插入第一个元素的索引
c - 集合，其中包含要添加到此列表中的元素
### Returns:
如果此列表因调用而更改，则为true
### Throws:
IndexOutOfBoundsException - 如果索引超出范围(索引< 0 ||索引>= size())
NullPointerException - 如果指定的集合为空








## removeRange
```java
    protected void removeRange(int fromIndex, int toIndex) {
        modCount++;
        int numMoved = size - toIndex;
        System.arraycopy(elementData, toIndex, elementData, fromIndex,
                         numMoved);

        // clear to let GC do its work
        int newSize = size - (toIndex-fromIndex);
        for (int i = newSize; i < size; i++) {
            elementData[i] = null;
        }
        size = newSize;
    }
```
从该列表中删除索引位于包含的fromIndex和排除的toIndex之间的所有元素。将任何后续元素向左移动(减少它们的索引)。这个调用通过(toIndex - fromIndex)元素缩短列表。(如果toIndex==fromIndex，则此操作无效。)
### Overrides:
removeRange in class AbstractList<E>
### Parameters:
fromIndex - 要删除的第一个元素的索引
toIndex - 删除最后一个元素后的索引
### Throws:
IndexOutOfBoundsException - 如果索引超出范围(索引< 0 ||索引>= size())








## rangeCheck
```java
    private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```
检查给定索引是否在范围内。如果没有，则抛出适当的运行时异常。这个方法不检查索引是否为负数:它总是在数组访问之前使用，如果索引为负数，数组访问将抛出一个ArrayIndexOutOfBoundsException。



```java
    private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```
由add和addAll使用的rangeCheck的一个版本。


```java
    private String outOfBoundsMsg(int index) {
        return "Index: "+index+", Size: "+size;
    }
```
构造IndexOutOfBoundsException详细信息。在错误处理代码的许多可能重构中，这种“大纲”在服务器和客户机vm上都表现得最好。






## removeAll
```java
    public boolean removeAll(Collection<?> c) {
        Objects.requireNonNull(c);
        return batchRemove(c, false);
    }
```
从该列表中删除指定集合中包含的所有元素。
### Specified by:
removeAll in interface Collection<E>
### Specified by:
removeAll in interface List<E>
### Overrides:
removeAll in class AbstractCollection<E>
### Parameters:
c - 集合，其中包含要从此列表中删除的元素
### Returns:
如果此列表因调用而更改，则为true
### Throws:
ClassCastException - 如果列表中元素的类与指定的集合不兼容(可选)
NullPointerException - 如果这个列表包含一个空元素，而指定的集合不允许空元素(可选)，或者指定的集合为空






## retainAll
```java
    public boolean retainAll(Collection<?> c) {
        Objects.requireNonNull(c);
        return batchRemove(c, true);
    }
    
    private boolean batchRemove(Collection<?> c, boolean complement) {
        final Object[] elementData = this.elementData;
        int r = 0, w = 0;
        boolean modified = false;
        try {
            for (; r < size; r++)
                if (c.contains(elementData[r]) == complement)
                    elementData[w++] = elementData[r];
        } finally {
            // Preserve behavioral compatibility with AbstractCollection,
            // even if c.contains() throws.
            if (r != size) {
                System.arraycopy(elementData, r,
                                 elementData, w,
                                 size - r);
                w += size - r;
            }
            if (w != size) {
                // clear to let GC do its work
                for (int i = w; i < size; i++)
                    elementData[i] = null;
                modCount += size - w;
                size = w;
                modified = true;
            }
        }
        return modified;
    }
```
只保留此列表中包含在指定集合中的元素。换句话说，从这个列表中删除指定集合中不包含的所有元素。
### Specified by:
retainAll in interface Collection<E>
### Specified by:
retainAll in interface List<E>
### Overrides:
retainAll in class AbstractCollection<E>
### Parameters:
c - 包含要保留在此列表中的元素的集合
### Returns:
如果此列表因调用而更改，则为true
### Throws:
ClassCastException - 如果列表中某个元素的类与指定的集合不兼容(可选)
NullPointerException - 如果这个列表包含一个空元素，而指定的集合不允许空元素(可选)，或者指定的集合为空
### See Also:
Collection.contains(Object)






## writeObject
```java
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException{
        // Write out element count, and any hidden stuff
        int expectedModCount = modCount;
        s.defaultWriteObject();

        // Write out size as capacity for behavioural compatibility with clone()
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (int i=0; i<size; i++) {
            s.writeObject(elementData[i]);
        }

        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
    }
```













## readObject
```java
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        elementData = EMPTY_ELEMENTDATA;

        // Read in size, and any hidden stuff
        s.defaultReadObject();

        // Read in capacity
        s.readInt(); // ignored

        if (size > 0) {
            // be like clone(), allocate array based upon size not capacity
            int capacity = calculateCapacity(elementData, size);
            SharedSecrets.getJavaOISAccess().checkArray(s, Object[].class, capacity);
            ensureCapacityInternal(size);

            Object[] a = elementData;
            // Read in all elements in the proper order.
            for (int i=0; i<size; i++) {
                a[i] = s.readObject();
            }
        }
    }
```












## listIterator
```java
    public ListIterator<E> listIterator(int index) {
        if (index < 0 || index > size)
            throw new IndexOutOfBoundsException("Index: "+index);
        return new ListItr(index);
    }
```
返回列表中元素的列表迭代器(按适当的顺序)，从列表中的指定位置开始。指定的索引指示将由对next的初始调用返回的第一个元素。对previous的初始调用将返回具有指定索引- 1的元素。

返回的列表迭代器是快速故障的。
### Specified by:
listIterator in interface List<E>
### Overrides:
listIterator in class AbstractList<E>
### Parameters:
index - 从列表迭代器返回的第一个元素的索引(通过调用next)
### Returns:
一个列表迭代器，从列表中指定的位置开始，遍历列表中的元素(按适当的顺序)
### Throws:
IndexOutOfBoundsException - 如果索引超出范围(索引< 0 ||索引>= size())







## listIterator
```java
    public ListIterator<E> listIterator() {
        return new ListItr(0);
    }
```
返回列表中元素的列表迭代器(按适当的顺序)。

返回的列表迭代器是快速故障的。
### Specified by:
listIterator in interface List<E>
### Overrides:
listIterator in class AbstractList<E>
### Returns:
列表迭代器(按适当的顺序)遍历列表中的元素
### See Also:
listIterator(int)






## iterator
```java
    public Iterator<E> iterator() {
        return new Itr();
    }
```
返回列表中元素的列表迭代器(按适当的顺序)。

返回的列表迭代器是快速故障的。
### Specified by:
iterator in interface Iterable<E>
### Specified by:
iterator in interface Collection<E>
### Specified by:
iterator in interface List<E>
### Overrides:
iterator in class AbstractList<E>
### Returns:
按适当顺序遍历列表中的元素的迭代器








## Itr
```java
    private class Itr implements Iterator<E> {
        int cursor;       // 返回的下一个元素的索引
        int lastRet = -1; // 上一个元素返回的索引； -1 if no such
        int expectedModCount = modCount;

        Itr() {} // 构造函数

        // 是否有下一个元素
        public boolean hasNext() {
            return cursor != size;
        }
        // 取到下一个元素
        @SuppressWarnings("unchecked")
        public E next() {
            checkForComodification();
            int i = cursor; // 当前索引
            if (i >= size)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }
        // 移除元素
        public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                ArrayList.this.remove(lastRet);
                cursor = lastRet;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }

        @Override
        @SuppressWarnings("unchecked")
        public void forEachRemaining(Consumer<? super E> consumer) {
            Objects.requireNonNull(consumer);
            final int size = ArrayList.this.size;
            int i = cursor;
            if (i >= size) {
                return;
            }
            final Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) {
                throw new ConcurrentModificationException();
            }
            while (i != size && modCount == expectedModCount) {
                consumer.accept((E) elementData[i++]);
            }
            // update once at end of iteration to reduce heap write traffic
            cursor = i;
            lastRet = i - 1;
            checkForComodification();
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
```







## ListItr
```java
    private class ListItr extends Itr implements ListIterator<E> {
    // 列表迭代器构造函数，可以传入开始索引
        ListItr(int index) {
            super();
            cursor = index;
        }
    // 是否有前一个值
        public boolean hasPrevious() {
            return cursor != 0;
        }
// 下一个值的索引
        public int nextIndex() {
            return cursor;
        }
// 前一个值索引
        public int previousIndex() {
            return cursor - 1;
        }
// 取到前一个值
        @SuppressWarnings("unchecked")
        public E previous() {
            checkForComodification();
            int i = cursor - 1;
            if (i < 0)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i;
            return (E) elementData[lastRet = i];
        }
// set当前的值
        public void set(E e) {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                ArrayList.this.set(lastRet, e);
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
// add值
        public void add(E e) {
            checkForComodification();

            try {
                int i = cursor;
                ArrayList.this.add(i, e);
                cursor = i + 1;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
    }
```












## subList
```java
// 获取子范围list
    public List<E> subList(int fromIndex, int toIndex) {
        subListRangeCheck(fromIndex, toIndex, size);
        return new SubList(this, 0, fromIndex, toIndex);
    }
// 范围检查
    static void subListRangeCheck(int fromIndex, int toIndex, int size) {
        if (fromIndex < 0)
            throw new IndexOutOfBoundsException("fromIndex = " + fromIndex);
        if (toIndex > size)
            throw new IndexOutOfBoundsException("toIndex = " + toIndex);
        if (fromIndex > toIndex)
            throw new IllegalArgumentException("fromIndex(" + fromIndex +
                                               ") > toIndex(" + toIndex + ")");
    }
// 实现类
    private class SubList extends AbstractList<E> implements RandomAccess {
        private final AbstractList<E> parent;
        private final int parentOffset;
        private final int offset;
        int size;
// 构造函数
        SubList(AbstractList<E> parent,
                int offset, int fromIndex, int toIndex) {
            this.parent = parent; // 传入list
            this.parentOffset = fromIndex; // 开始索引
            this.offset = offset + fromIndex; // 偏移量
            this.size = toIndex - fromIndex; // 截取大小
            this.modCount = ArrayList.this.modCount;
        }
// 设置值
        public E set(int index, E e) {
            rangeCheck(index);
            checkForComodification();
            E oldValue = ArrayList.this.elementData(offset + index);
            ArrayList.this.elementData[offset + index] = e;
            return oldValue;
        }
// 获取元素
        public E get(int index) {
            rangeCheck(index);
            checkForComodification();
            return ArrayList.this.elementData(offset + index);
        }
// 返回大小
        public int size() {
            checkForComodification();
            return this.size;
        }
// 添加元素，指定索引
        public void add(int index, E e) {
            rangeCheckForAdd(index);
            checkForComodification();
            parent.add(parentOffset + index, e);
            this.modCount = parent.modCount;
            this.size++;
        }
// 移除元素
        public E remove(int index) {
            rangeCheck(index);
            checkForComodification();
            E result = parent.remove(parentOffset + index);
            this.modCount = parent.modCount;
            this.size--;
            return result;
        }
// 移除范围内元素
        protected void removeRange(int fromIndex, int toIndex) {
            checkForComodification();
            parent.removeRange(parentOffset + fromIndex,
                               parentOffset + toIndex);
            this.modCount = parent.modCount;
            this.size -= toIndex - fromIndex;
        }
// 添加集合
        public boolean addAll(Collection<? extends E> c) {
            return addAll(this.size, c);
        }
// 添加集合，指定位置
        public boolean addAll(int index, Collection<? extends E> c) {
            rangeCheckForAdd(index);
            int cSize = c.size();
            if (cSize==0)
                return false;

            checkForComodification();
            parent.addAll(parentOffset + index, c);
            this.modCount = parent.modCount;
            this.size += cSize;
            return true;
        }
// 返回列表迭代器
        public Iterator<E> iterator() {
            return listIterator();
        }

        public ListIterator<E> listIterator(final int index) {
            checkForComodification();
            rangeCheckForAdd(index);
            final int offset = this.offset;

            return new ListIterator<E>() {
                int cursor = index;
                int lastRet = -1;
                int expectedModCount = ArrayList.this.modCount;

                public boolean hasNext() {
                    return cursor != SubList.this.size;
                }

                @SuppressWarnings("unchecked")
                public E next() {
                    checkForComodification();
                    int i = cursor;
                    if (i >= SubList.this.size)
                        throw new NoSuchElementException();
                    Object[] elementData = ArrayList.this.elementData;
                    if (offset + i >= elementData.length)
                        throw new ConcurrentModificationException();
                    cursor = i + 1;
                    return (E) elementData[offset + (lastRet = i)];
                }

                public boolean hasPrevious() {
                    return cursor != 0;
                }

                @SuppressWarnings("unchecked")
                public E previous() {
                    checkForComodification();
                    int i = cursor - 1;
                    if (i < 0)
                        throw new NoSuchElementException();
                    Object[] elementData = ArrayList.this.elementData;
                    if (offset + i >= elementData.length)
                        throw new ConcurrentModificationException();
                    cursor = i;
                    return (E) elementData[offset + (lastRet = i)];
                }

                @SuppressWarnings("unchecked")
                public void forEachRemaining(Consumer<? super E> consumer) {
                    Objects.requireNonNull(consumer);
                    final int size = SubList.this.size;
                    int i = cursor;
                    if (i >= size) {
                        return;
                    }
                    final Object[] elementData = ArrayList.this.elementData;
                    if (offset + i >= elementData.length) {
                        throw new ConcurrentModificationException();
                    }
                    while (i != size && modCount == expectedModCount) {
                        consumer.accept((E) elementData[offset + (i++)]);
                    }
                    // update once at end of iteration to reduce heap write traffic
                    lastRet = cursor = i;
                    checkForComodification();
                }

                public int nextIndex() {
                    return cursor;
                }

                public int previousIndex() {
                    return cursor - 1;
                }

                public void remove() {
                    if (lastRet < 0)
                        throw new IllegalStateException();
                    checkForComodification();

                    try {
                        SubList.this.remove(lastRet);
                        cursor = lastRet;
                        lastRet = -1;
                        expectedModCount = ArrayList.this.modCount;
                    } catch (IndexOutOfBoundsException ex) {
                        throw new ConcurrentModificationException();
                    }
                }

                public void set(E e) {
                    if (lastRet < 0)
                        throw new IllegalStateException();
                    checkForComodification();

                    try {
                        ArrayList.this.set(offset + lastRet, e);
                    } catch (IndexOutOfBoundsException ex) {
                        throw new ConcurrentModificationException();
                    }
                }

                public void add(E e) {
                    checkForComodification();

                    try {
                        int i = cursor;
                        SubList.this.add(i, e);
                        cursor = i + 1;
                        lastRet = -1;
                        expectedModCount = ArrayList.this.modCount;
                    } catch (IndexOutOfBoundsException ex) {
                        throw new ConcurrentModificationException();
                    }
                }

                final void checkForComodification() {
                    if (expectedModCount != ArrayList.this.modCount)
                        throw new ConcurrentModificationException();
                }
            };
        }

        public List<E> subList(int fromIndex, int toIndex) {
            subListRangeCheck(fromIndex, toIndex, size);
            return new SubList(this, offset, fromIndex, toIndex);
        }

        private void rangeCheck(int index) {
            if (index < 0 || index >= this.size)
                throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
        }

        private void rangeCheckForAdd(int index) {
            if (index < 0 || index > this.size)
                throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
        }

        private String outOfBoundsMsg(int index) {
            return "Index: "+index+", Size: "+this.size;
        }

        private void checkForComodification() {
            if (ArrayList.this.modCount != this.modCount)
                throw new ConcurrentModificationException();
        }

        public Spliterator<E> spliterator() {
            checkForComodification();
            return new ArrayListSpliterator<E>(ArrayList.this, offset,
                                               offset + this.size, this.modCount);
        }
    }
```
返回列表中指定的包含的fromIndex和包含的toIndex之间的部分的视图。(如果fromIndex和toIndex相等，则返回的列表为空。)返回的列表由该列表支持，因此返回列表中的非结构性更改将反映在该列表中，反之亦然。返回的列表支持所有可选列表操作。

这种方法不需要显式的范围操作(数组中常见的那种)。任何需要列表的操作都可以作为范围操作使用，方法是传递子列表视图而不是整个列表。例如，下面的习语从列表中删除一系列元素:
```java
      list.subList(from, to).clear();
```
可以为indexOf(Object)和lastIndexOf(Object)构造类似的习惯用法，集合类中的所有算法都可以应用于子列表。

如果支持listis以除通过返回列表之外的任何方式进行结构修改，则此方法返回的列表的语义将变得未定义。(结构修改是指改变列表的大小，或者以一种正在进行的迭代可能产生错误结果的方式扰乱列表。)
### Specified by:
subList in interface List<E>
### Overrides:
subList in class AbstractList<E>
### Parameters:
fromIndex - 子列表的低端(包括)
toIndex - 子列表的高端点(排除)
### Returns:
此列表中指定范围的视图
### Throws:
IndexOutOfBoundsException - 如果端点索引值超出范围(从mindex < 0 ||到索引>大小)
IllegalArgumentException - 如果端点索引不正常(从mindex >到index)











## forEach
```java
@Overridepublic void forEach(Consumer<? super E> action) {
    Objects.requireNonNull(action);
    final int expectedModCount = modCount;
    @SuppressWarnings("unchecked")
    final E[] elementData = (E[]) this.elementData;
    final int size = this.size;
    for (int i=0; modCount == expectedModCount && i < size; i++) {
        action.accept(elementData[i]);
    }
    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
}
```
为可迭代的每个元素执行给定的操作，直到处理完所有元素或操作引发异常为止。除非实现类另有指定，否则操作将按迭代顺序执行(如果指定了迭代顺序)。该操作引发的异常将传递给调用者。
### Specified by:
forEach in interface Iterable<E>
### Parameters:
action - 为每个元素执行的操作













## spliterator
```java
    @Override
    public Spliterator<E> spliterator() {
        return new ArrayListSpliterator<>(this, 0, -1, 0);
    }
```

```java
    static final class ArrayListSpliterator<E> implements Spliterator<E> {

        /*
         * If ArrayLists were immutable, or structurally immutable (no
         * adds, removes, etc), we could implement their spliterators
         * with Arrays.spliterator. Instead we detect as much
         * interference during traversal as practical without
         * sacrificing much performance. We rely primarily on
         * modCounts. These are not guaranteed to detect concurrency
         * violations, and are sometimes overly conservative about
         * within-thread interference, but detect enough problems to
         * be worthwhile in practice. To carry this out, we (1) lazily
         * initialize fence and expectedModCount until the latest
         * point that we need to commit to the state we are checking
         * against; thus improving precision.  (This doesn't apply to
         * SubLists, that create spliterators with current non-lazy
         * values).  (2) We perform only a single
         * ConcurrentModificationException check at the end of forEach
         * (the most performance-sensitive method). When using forEach
         * (as opposed to iterators), we can normally only detect
         * interference after actions, not before. Further
         * CME-triggering checks apply to all other possible
         * violations of assumptions for example null or too-small
         * elementData array given its size(), that could only have
         * occurred due to interference.  This allows the inner loop
         * of forEach to run without any further checks, and
         * simplifies lambda-resolution. While this does entail a
         * number of checks, note that in the common case of
         * list.stream().forEach(a), no checks or other computation
         * occur anywhere other than inside forEach itself.  The other
         * less-often-used methods cannot take advantage of most of
         * these streamlinings.
         */

        private final ArrayList<E> list;
        private int index; // current index, modified on advance/split
        private int fence; // -1 until used; then one past last index
        private int expectedModCount; // initialized when fence set

        /** Create new spliterator covering the given  range */
        ArrayListSpliterator(ArrayList<E> list, int origin, int fence,
                             int expectedModCount) {
            this.list = list; // OK if null unless traversed
            this.index = origin;
            this.fence = fence;
            this.expectedModCount = expectedModCount;
        }

        private int getFence() { // initialize fence to size on first use
            int hi; // (a specialized variant appears in method forEach)
            ArrayList<E> lst;
            if ((hi = fence) < 0) {
                if ((lst = list) == null)
                    hi = fence = 0;
                else {
                    expectedModCount = lst.modCount;
                    hi = fence = lst.size;
                }
            }
            return hi;
        }

        public ArrayListSpliterator<E> trySplit() {
            int hi = getFence(), lo = index, mid = (lo + hi) >>> 1;
            return (lo >= mid) ? null : // divide range in half unless too small
                new ArrayListSpliterator<E>(list, lo, index = mid,
                                            expectedModCount);
        }

        public boolean tryAdvance(Consumer<? super E> action) {
            if (action == null)
                throw new NullPointerException();
            int hi = getFence(), i = index;
            if (i < hi) {
                index = i + 1;
                @SuppressWarnings("unchecked") E e = (E)list.elementData[i];
                action.accept(e);
                if (list.modCount != expectedModCount)
                    throw new ConcurrentModificationException();
                return true;
            }
            return false;
        }

        public void forEachRemaining(Consumer<? super E> action) {
            int i, hi, mc; // hoist accesses and checks from loop
            ArrayList<E> lst; Object[] a;
            if (action == null)
                throw new NullPointerException();
            if ((lst = list) != null && (a = lst.elementData) != null) {
                if ((hi = fence) < 0) {
                    mc = lst.modCount;
                    hi = lst.size;
                }
                else
                    mc = expectedModCount;
                if ((i = index) >= 0 && (index = hi) <= a.length) {
                    for (; i < hi; ++i) {
                        @SuppressWarnings("unchecked") E e = (E) a[i];
                        action.accept(e);
                    }
                    if (lst.modCount == mc)
                        return;
                }
            }
            throw new ConcurrentModificationException();
        }

        public long estimateSize() {
            return (long) (getFence() - index);
        }

        public int characteristics() {
            return Spliterator.ORDERED | Spliterator.SIZED | Spliterator.SUBSIZED;
        }
    }
```
在此列表中的元素上创建延迟绑定和故障快速Spliterator。
### Specified by:
spliterator in interface Iterable<E>
### Specified by:
spliterator in interface Collection<E>
### Specified by:
spliterator in interface List<E>
### Returns:
对列表中的元素使用Spliterator
### Since 1.8













## removeIf
```java
    @Override
    public boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter);
        // figure out which elements are to be removed
        // any exception thrown from the filter predicate at this stage
        // will leave the collection unmodified
        int removeCount = 0;
        final BitSet removeSet = new BitSet(size);
        final int expectedModCount = modCount;
        final int size = this.size;
        for (int i=0; modCount == expectedModCount && i < size; i++) {
            @SuppressWarnings("unchecked")
            final E element = (E) elementData[i];
            if (filter.test(element)) {
                removeSet.set(i);
                removeCount++;
            }
        }
        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }

        // shift surviving elements left over the spaces left by removed elements
        final boolean anyToRemove = removeCount > 0;
        if (anyToRemove) {
            final int newSize = size - removeCount;
            for (int i=0, j=0; (i < size) && (j < newSize); i++, j++) {
                i = removeSet.nextClearBit(i);
                elementData[j] = elementData[i];
            }
            for (int k=newSize; k < size; k++) {
                elementData[k] = null;  // Let gc do its work
            }
            this.size = newSize;
            if (modCount != expectedModCount) {
                throw new ConcurrentModificationException();
            }
            modCount++;
        }

        return anyToRemove;
    }
```
移除此集合中满足给定谓词的所有元素。迭代期间或谓词抛出的错误或运行时异常将传递给调用者。
### Specified by:
removeIf in interface Collection<E>
### Parameters:
filter - 一个谓词，它返回true，用于删除元素
### Returns:
如果删除了任何元素，则为真














## replaceAll
```java
    @Override
    @SuppressWarnings("unchecked")
    public void replaceAll(UnaryOperator<E> operator) {
        Objects.requireNonNull(operator);
        final int expectedModCount = modCount;
        final int size = this.size;
        for (int i=0; modCount == expectedModCount && i < size; i++) {
            elementData[i] = operator.apply((E) elementData[i]);
        }
        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
        modCount++;
    }
```
将运算符应用于该元素的结果替换此列表中的每个元素。操作符抛出的错误或运行时异常将传递给调用者。
### Specified by:
replaceAll in interface List<E>
### Parameters:
operator - 应用于每个元素的运算符










## sort
```java
    @Override
    @SuppressWarnings("unchecked")
    public void sort(Comparator<? super E> c) {
        final int expectedModCount = modCount;
        Arrays.sort((E[]) elementData, 0, size, c);
        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
        modCount++;
    }
```
根据指定比较器所诱导的顺序对该列表进行排序。

此列表中的所有元素必须使用指定的比较器相互比较(即，c.compare(e1, e2)不能为列表中的任何元素e1和e2抛出ClassCastException)。

如果指定的比较器为null，则此列表中的所有元素必须实现Comparable接口，并且应该使用元素的自然顺序。

此列表必须可修改，但不需要调整大小。
### Specified by:
sort in interface List<E>
### Parameters:
c - 用于比较列表元素的比较器。空值表示应该使用元素的自然顺序