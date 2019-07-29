---
layout:     post
title:      JDK源码之function包（未完）
subtitle:   JDK源码深度解析
date:       2019-07-28
author:     渣子哥
header-img: img/pexels-photo-1936936.jpeg
catalog: true
tags:
    - JDK8源码
    - 函数式编程
---

# 函数式编程






# BiConsumer

```java
public interface BiConsumer<T, U>
```



#### accept

```java
void accept(T t, U u);
```



#### andThen

```java
default BiConsumer<T, U> andThen(BiConsumer<? super T, ? super U> after) {
        Objects.requireNonNull(after);

        return (l, r) -> {
            accept(l, r);
            after.accept(l, r);
        };
}
```



# BiFunction

```java
public interface BiFunction<T, U, R>
```



#### apply

```java
R apply(T t, U u);
```



#### andThen

```java
default <V> BiFunction<T, U, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t, U u) -> after.apply(apply(t, u));
}
```



# BinaryOperator

```java
public interface BinaryOperator<T> extends BiFunction<T,T,T>
```



#### minBy

```java
public static <T> BinaryOperator<T> minBy(Comparator<? super T> comparator) {
        Objects.requireNonNull(comparator);
        return (a, b) -> comparator.compare(a, b) <= 0 ? a : b;
}
```



#### maxBy

```java
public static <T> BinaryOperator<T> maxBy(Comparator<? super T> comparator) {
        Objects.requireNonNull(comparator);
        return (a, b) -> comparator.compare(a, b) >= 0 ? a : b;
}
```



# BiPredicate

```java
public interface BiPredicate<T, U>
```



#### test

```java
boolean test(T t, U u);
```



#### and

```java
default BiPredicate<T, U> and(BiPredicate<? super T, ? super U> other) {
        Objects.requireNonNull(other);
        return (T t, U u) -> test(t, u) && other.test(t, u);
}
```



#### negate

```java
default BiPredicate<T, U> negate() {
        return (T t, U u) -> !test(t, u);
}
```



#### or

```java
default BiPredicate<T, U> or(BiPredicate<? super T, ? super U> other) {
        Objects.requireNonNull(other);
        return (T t, U u) -> test(t, u) || other.test(t, u);
}
```



# BooleanSupplier

```java
public interface BooleanSupplier
```



#### getAsBoolean

```java
boolean getAsBoolean();
```



# Consumer

```java
public interface Consumer<T>
```



#### accept

```java
void accept(T t);
```



#### andThen

```java
default Consumer<T> andThen(Consumer<? super T> after) {
    Objects.requireNonNull(after);
    return (T t) -> { accept(t); after.accept(t); };
}
```



# DoubleBinaryOperator

```java
public interface DoubleBinaryOperator
```



#### applyAsDouble

```java
double applyAsDouble(double left, double right);
```



# DoubleConsumer

```java
public interface DoubleConsumer
```



#### accept

```java
void accept(double value);
```



#### andThen

```java
default DoubleConsumer andThen(DoubleConsumer after) {
    Objects.requireNonNull(after);
    return (double t) -> { accept(t); after.accept(t); };
}
```



# DoubleFunction

```java
public interface DoubleFunction<R>
```



#### apply

```java
R apply(double value);
```



# DoublePredicate

```java
public interface DoublePredicate
```



#### test

```java
boolean test(double value);
```



#### and

```java
default DoublePredicate and(DoublePredicate other) {
    Objects.requireNonNull(other);
    return (value) -> test(value) && other.test(value);
}
```



#### negate

```java
default DoublePredicate negate() {
    return (value) -> !test(value);
}
```



#### or

```java
default DoublePredicate or(DoublePredicate other) {
    Objects.requireNonNull(other);
    return (value) -> test(value) || other.test(value);
}
```



# DoubleSupplier

```java
public interface DoubleSupplier
```



#### getAsDouble

```java
double getAsDouble();
```



# DoubleToIntFunction

```java
public interface DoubleToIntFunction
```



#### applyAsInt

```java
int applyAsInt(double value);
```



# DoubleToLongFunction

```java
public interface DoubleToLongFunction
```



#### applyAsLong

```java
long applyAsLong(double value);
```



# DoubleUnaryOperator

```java
public interface DoubleUnaryOperator
```



#### applyAsDouble

```java
double applyAsDouble(double operand);
```



#### compose

```java
default DoubleUnaryOperator compose(DoubleUnaryOperator before) {
    Objects.requireNonNull(before);
    return (double v) -> applyAsDouble(before.applyAsDouble(v));
}
```



#### andThen

```java
default DoubleUnaryOperator andThen(DoubleUnaryOperator after) {
    Objects.requireNonNull(after);
    return (double t) -> after.applyAsDouble(applyAsDouble(t));
}
```



#### identity

```java
static DoubleUnaryOperator identity() {
        return t -> t;
}
```



# Function

```java
public interface Function<T, R>
```



#### apply

```java
R apply(T t);
```



#### compose

```java
default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
}
```



#### andThen

```java
default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
}
```



#### identity

```java
static <T> Function<T, T> identity() {
        return t -> t;
}
```



