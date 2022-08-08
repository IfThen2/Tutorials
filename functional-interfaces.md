# Java Functional Interfaces Cheat-Sheet

## Table of Contents

- [Java Functional Interfaces Cheat-Sheet](#java-functional-interfaces-cheat-sheet)
  * [1.0 Overview:](#10-overview-)
  * [2.0 Function `T -> R`](#20-function--t----r-)
    + [2.1 Primitive Functions](#21-primitive-functions)
      * [Figure 2.1](#figure-21)
    + [2.2 Two-Arity Functions `(T, U) -> R`](#22-two-arity-functions---t--u-----r-)
    + [2.3 Primitive Two-Arity Functions](#23-primitive-two-arity-functions)
      * [Figure 2.2](#figure-22)
    + [2.4 Function Reference Examples](#24-function-reference-examples)
  * [3.0 Supplier `() -> T`](#30-supplier--------t-)
    + [3.1 Primitive Suppliers](#31-primitive-suppliers)
      * [Figure 3.1](#figure-31)
    + [3.2 Supplier Reference Examples](#32-supplier-reference-examples)
  * [4.0 Consumer `T -> void`](#40-consumer--t----void-)
    + [4.1 Primitive Consumers](#41-primitive-consumers)
      * [Figure 4.1](#figure-41)
    + [4.2 Two-Arity Consumers `(T, U) -> void`](#42-two-arity-consumers---t--u-----void-)
    + [4.3 Primitive Two-Arity Consumers](#43-primitive-two-arity-consumers)
      * [Figure 4.2](#figure-42)
    + [4.4 Consumer Reference Examples](#44-consumer-reference-examples)
  * [5.0 Predicate `T -> boolean`](#50-predicate--t----boolean-)
    * [Figure 5.1](#figure-51)
    + [5.1 Primitive Predicates](#51-primitive-predicates)
      * [Figure 5.2](#figure-52)
    + [5.2 Two-Arity Predicates `(T, U) -> boolean`](#52-two-arity-predicates---t--u-----boolean-)
    + [5.3 Primitive Two-Arity Predicates](#53-primitive-two-arity-predicates)
    + [5.4 Predicate Reference Examples](#54-predicate-reference-examples)
  * [6.0 Operator](#60-operator)
    + [6.1 UnaryOperator `T -> T`](#61-unaryoperator--t----t-)
    + [6.2 Primitive UnaryOperator](#62-primitive-unaryoperator)
      * [Figure 6.1](#figure-61)
    + [6.3 BinaryOperator `(T, T) -> T`](#63-binaryoperator---t--t-----t-)
    + [6.4 Primitive BinaryOperator](#64-primitive-binaryoperator)
      * [Figure 6.2](#figure-62)
    + [6.5 Operator Reference Examples](#65-operator-reference-examples)
  * [7.0 Bonus Goodies](#70-bonus-goodies)
    + [7.1 1-arity JDK Provided Functional Interface Table](#71-1-arity-jdk-provided-functional-interface-table)
      * [Figure 7.1](#figure-71)
    + [7.2 Legacy Functional Interfaces](#72-legacy-functional-interfaces)
  
---

## 1.0 Overview:

Any `interface` with a SAM (Single abstract method) is a _functional interface._

```java
@FunctionalInterface
public interface MyFunctionalInterface {
    public void doSomething();
}
```

The `@FunctionalInterface` annotation is preferred but not required. It can be helpful to add, however, as in addition to clarifying intent, it also allows the compiler to generate errors when the requirements for a functional interface are not met.

Methods signatures with `default` and `static` modifiers are not abstract, and as such a functional interface can have multiple `default` or `static` methods.

```java
@FunctionalInterface
public interface MyFunctionalInterface {

    public void doSomething();

    public default void doSomethingElse() {
        System.out.println("Something Else");
    }

    public static String getSomethingElse() {
        return "SomethingElse";
    }
}
```

Functional interfaces can be implemented with a concrete class.

```java
public class SayHello implements MyFunctionalInterface
 {
    @Override
    public void doSomething() {
         System.out.println("Something");
    }
}
```

They can also be implemented by a _lambda_ expression.

```java
MyFunctionalInterface saySomething = () -> {
    System.out.println("Something");
}
```

There are a multitude of functional interfaces included in the core Java library's `java.util.function` package, but you can also create your own to suit any more specific needs you may have, as will be demonstrated further in the following sections.

---

## 2.0 Function `T -> R`

The simplest and most general functional Interface is the `Function` interface itself which is parameterized by the types of its argument `T` and a return value `R`.

```java
public interface Function<T, R> { … }
```

The single unimplemented method for `Function` has the following signature.

```java
R apply(T t);
```

`Function` is commonly used for _mapping_ objects of one type to another.

### 2.1 Primitive Functions

Since a primitive type can’t be a generic type argument, there are versions of the `Function` interface for various combinations of argument and return types for commonly used primitives `double`, `int`, and `long`.

##### Figure 2.1

|            | **int**               | **long**               | **double**               | **generic**      |      
|:----------:|:---------------------:|:----------------------:|:------------------------:|:----------------:| 
| **int**    | **X**                 | _IntToLongFunction_    | _IntToDoubleFunction_    | _IntFunction_    |        
| **long**   | _LongToIntFunction_   | **X**                  | _LongToDoubleFunction_   | _LongFunction_   |        
| **double** | _DoubleToIntFunction_ | _DoubleToLongFunction_ | **X**                    | _DoubleFunction_ |      
| **generic**| _ToIntFunction_       | _ToLongFunction_       | _ToDoubleFunction_       | **X**            |

### 2.2 Two-Arity Functions `(T, U) -> R`

In some situations we may want to define a function with *two* arguments instead of one. Luckily, Java provides functional interfaces for such scenarios as a part of the JDK. The generic two-arity version of `Function` is `BiFunction`. It is parameterized by the types of each argument `T` and `U` separately, as well as the return type `R`.

```java
public interface BiFunction<T, U, R> { … }
```

The single unimplemented method for `BiFunction` has the following signature.

```java
R apply(T t, U u);
```

### 2.3 Primitive Two-Arity Functions

There are versions of the `BiFunction` interface for commonly used primitive types `double`, `int`, and `long`.

##### Figure 2.2

|             | int               | long               | double               |
|:-----------:|:-----------------:|:------------------:|:--------------------:|
| **generic** | _ToIntBiFunction_ | _ToLongBiFunction_ | _ToDoubleBiFunction_ |

Note that there are no two-arity specializations of `BiFunction` that accept primitive argument types _(only primitive return values)_. If such a need arises, we must create those functional interfaces ourselves.

```java
/**
* Represents a function that accepts a long argument and produces a generic result. 
*/
@FunctionalInterface
public interface LongBiFunction<R> {

    R apply(long value);
}
```

### 2.4 Function Reference Examples

The following are useful reference examples either from the core Java library, or from industry standard libraries. They can give you a good starting point into understanding which scenarios `Function` might be useful.

The `Stream` interface, and it's implementation in `ReferencePipeline` use `Function` for mapping a `Stream` of one type (`T`) to another (`R`). 

*java/util/stream/ReferencePipeline.java* _184_
```java
/**
 * Returns a stream consisting of the results of applying the given
 * function to the elements of this stream.
 *
 * <p>This is an <a href="package-summary.html#StreamOps">intermediate
 * operation</a>.
 *
 * @param <R> The element type of the new stream
 * @param mapper a <a href="package-summary.html#NonInterference">non-interfering</a>,
 *               <a href="package-summary.html#Statelessness">stateless</a>
 *               function to apply to each element
 * @return the new stream
 */
<R> Stream<R> map(Function<? super T, ? extends R> mapper);

/// ...

@Override
@SuppressWarnings("unchecked")
public final <R> Stream<R> map(Function<? super P_OUT, ? extends R> mapper) {
    Objects.requireNonNull(mapper);
    return new StatelessOp<P_OUT, R>(this, StreamShape.REFERENCE,
                                 StreamOpFlag.NOT_SORTED | StreamOpFlag.NOT_DISTINCT) {
        @Override
        Sink<P_OUT> opWrapSink(int flags, Sink<R> sink) {
            return new Sink.ChainedReference<P_OUT, R>(sink) {
                @Override
                public void accept(P_OUT u) {
                    downstream.accept(mapper.apply(u));
                }
            };
        }
    };
}
```

The `Arrays` class has a `static` method which accepts a `Function` to set all of the elements of an array at once. The `Integer` indices for each element are the inputs to the `Function`.

*java/util/Arrays.java* _4695_
```java
/**
 * Set all elements of the specified array, using the provided
 * generator function to compute each element.
 *
 * <p>If the generator function throws an exception, it is relayed to
 * the caller and the array is left in an indeterminate state.
 *
 * @param <T> type of elements of the array
 * @param array array to be initialized
 * @param generator a function accepting an index and producing the desired
 *        value for that position
 * @throws NullPointerException if the generator is null
 * @since 1.8
 */
public static <T> void setAll(T[] array, IntFunction<? extends T> generator) {
    Objects.requireNonNull(generator);
    for (int i = 0; i < array.length; i++)
        array[i] = generator.apply(i);
}
```

---

## 3.0 Supplier `() -> T`

The Supplier interface is a specialization of `Function` which takes no arguments *(nullary function)* but still returns a value. It is parameterized only by the return type `T`.

```java
public interface Supplier<T> { … }
```

The single unimplemented method for `Supplier` has the following signature.

```java
T get();
```

By far the most common use case for `Supplier` is lazy value initialization/generation. This can be useful if we have an object that is expensive to create.

```java
Supplier<myObject> objectSupplier = () -> new myObject());
```

### 3.1 Primitive Suppliers

Similar to `Function`, there are versions of the `Supplier` interface for commonly used primitive types `double`, `int`, and `long`. Breaking from the established pattern, `Supplier` has one additional primitive specialization for the `boolean` data type.

##### Figure 3.1

|          | **int**         | **long**         | **double**         | **boolean**         |
|:--------:|:---------------:|:----------------:|:------------------:|:-------------------:|
| **void** | _IntSupplier_   | _LongSupplier_   | _DoubleSupplier_   | _BooleanSupplier_   |

### 3.2 Supplier Reference Examples

The following are useful reference examples either from the core Java library, or from industry standard libraries. They can give you a good starting point into understanding which scenarios `Supplier` might be useful.

In Java's logging API, `Supplier` is used for lazy value initialization. There is no need to construct the `String` log message if it might not end up being logged.

*java/util/logging/Logger.java* _805_

```java
/**
 * Log a message, which is only to be constructed if the logging level
 * is such that the message will actually be logged.
 * <p>
 * If the logger is currently enabled for the given message
 * level then the message is constructed by invoking the provided
 * supplier function and forwarded to all the registered output
 * Handler objects.
 * <p>
 * @param   level   One of the message level identifiers, e.g., SEVERE
 * @param   msgSupplier   A function, which when called, produces the
 *                        desired log message
 * @since 1.8
 */
public void log(Level level, Supplier<String> msgSupplier) {
    if (!isLoggable(level)) {
        return;
    }
    LogRecord lr = new LogRecord(level, msgSupplier.get());
    doLog(lr);
}
```

Starting with JUnit 5, The `Assertions` class allows you to provide a `Supplier<String>` instead of a `String` literal error message. The actual `String` message isn't constructed until necessary, and sometimes not even at all *(lazy initialization)*.

*org/junit/jupiter/api/Assertions* _950_

```java
/**
 * <em>Assert</em> that {@code expected} and {@code actual} are equal.
 * <p>Equality imposed by this method is consistent with {@link Double#equals(Object)} and
 * {@link Double#compare(double, double)}.
 * <p>If necessary, the failure message will be retrieved lazily from the supplied {@code messageSupplier}.
 */
static void assertEquals(double expected, double actual, Supplier<String> messageSupplier) {
    if (!doublesAreEqual(expected, actual)) {
        failNotEqual(expected, actual, messageSupplier);
    }
}

// ...ommitted code

static void failNotEqual(Object expected, Object actual, Supplier<String> messageSupplier) {
    fail(format(expected, actual, nullSafeGet(messageSupplier)), expected, actual);
}

// ...ommitted code

static String nullSafeGet(Supplier<String> messageSupplier) {
    return (messageSupplier != null ? messageSupplier.get() : null);
}
```

With a `CompleteableFuture` you can asynchronously retrieve a value from a `Supplier`.

*java/util/concurrent/CompleteableFuture* _1813_

```java
/**
 * Returns a new CompletableFuture that is asynchronously completed
 * by a task running in the {@link ForkJoinPool#commonPool()} with
 * the value obtained by calling the given Supplier.
 *
 * @param supplier a function returning the value to be used
 * to complete the returned CompletableFuture
 * @param <U> the function's return type
 * @return the new CompletableFuture
 */
  public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier) {
    return asyncSupplyStage(asyncPool, supplier);
  }
```

---

## 4.0 Consumer `T -> void`

The `Consumer` interface accepts a generified argument and returns nothing(`void`). It can be thought of as the opposite of `Supplier`. It is parameterized only by the type of it's argument `T`.

```java
public interface Consumer<T> { … }
```

The single unimplemented method for `Consumer` has the following signature.

```java
void accept(T t);
```

There are a multitude of functional interfaces that are useful when you want a *value*, but `Consumer` is most useful when you want an *action*. In fact, as you'll see in the reference examples, `Consumer` variables almost always have *"action"* as a part of their name.

```java
Consumer<String> stringPrinter = s -> System.out.println(s);
```

### 4.1 Primitive Consumers

Once again there are versions of the `Consumer` interface for commonly used primitive types `double`, `int`, and `long`.

##### Figure 4.1

|            | **void**           |
|:----------:|:------------------:|
| **int**    | _IntConsumer_      |
| **long**   | _LongConsumer_     |
| **double** | _DoubleConsumer_   |

### 4.2 Two-Arity Consumers `(T, U) -> void`

The generic two-arity version of `Consumer` is `BiConsumer`. The argument types do not need to match since `BiConsumer` parameterized by the types of each argument `T` and `U` separately.

```java
public interface BiConsumer<T, U> { … }
```

`BiConsumer` has the following **SAM** signature:

```java
void accept(T t, U u);
```

### 4.3 Primitive Two-Arity Consumers

Again there are versions of the `BiConsumer` interface for the most used primitive types `double`, `int`, and `long`. Note that only one of the arguments is generified, while the other is of the primitive type specified.

##### Figure 4.2

|            | **void**              |
|:----------:|:---------------------:|
| **int**    | _ObjIntConsumer_      |
| **long**   | _ObjLongConsumer_     |
| **double** | _ObjDoubleConsumer_   |

### 4.4 Consumer Reference Examples

The following are useful reference examples either from the core Java library, or from industry standard libraries. They can give you a good starting point into understanding which scenarios `Consumer` might be useful.

`Iterable` (*and thus out favorite collection classes*) uses `Consumer` for all of the `forEach()` type methods, when an action needs to be performed on every element.

*java/lang/Iterable.java* _72_
```java
/**
 * Performs the given action for each element of the {@code Iterable}
 * until all elements have been processed or the action throws an
 * exception.  Unless otherwise specified by the implementing class,
 * actions are performed in the order of iteration (if an iteration order
 * is specified).  Exceptions thrown by the action are relayed to the
 * caller.
 *
 * @implSpec
 * <p>The default implementation behaves as if:
 * <pre>{@code
 *     for (T t : this)
 *         action.accept(t);
 * }</pre>
 *
 * @param action The action to be performed for each element
 * @throws NullPointerException if the specified action is null
 * @since 1.8
 */
default void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);
    for (T t : this) {
        action.accept(t);
    }
}
```

`Map` utilizes a `BiConsumer` in its' `forEach()` method, in order to accept both the *key* and *value* to perform an action on.

*java/util/Map.java* _72_
```java
/**
 * Performs the given action for each entry in this map until all entries
 * have been processed or the action throws an exception.   Unless
 * otherwise specified by the implementing class, actions are performed in
 * the order of entry set iteration (if an iteration order is specified.)
 * Exceptions thrown by the action are relayed to the caller.
 *
 * @implSpec
 * The default implementation is equivalent to, for this {@code map}:
 * <pre> {@code
 * for (Map.Entry<K, V> entry : map.entrySet())
 *     action.accept(entry.getKey(), entry.getValue());
 * }</pre>
 *
 * The default implementation makes no guarantees about synchronization
 * or atomicity properties of this method. Any implementation providing
 * atomicity guarantees must override this method and document its
 * concurrency properties.
 *
 * @param action The action to be performed for each entry
 * @throws NullPointerException if the specified action is null
 * @throws ConcurrentModificationException if an entry is found to be
 * removed during iteration
 * @since 1.8
 */
default void forEach(BiConsumer<? super K, ? super V> action) {
    Objects.requireNonNull(action);
    for (Map.Entry<K, V> entry : entrySet()) {
        K k;
        V v;
        try {
            k = entry.getKey();
            v = entry.getValue();
        } catch(IllegalStateException ise) {
            // this usually means the entry is no longer in the map.
            throw new ConcurrentModificationException(ise);
        }
        action.accept(k, v);
    }
}
```


---

## 5.0 Predicate `T -> boolean`

The `Predicate` functional interface is a specialization of `Function` that receives a generified value and returns a `boolean`. It is parameterized by its argument type `T`.

```java
public interface Predicate<T> { … }
```

`Predicate` has the following unimplemented method.

```java
boolean test(T t);
```

A `Predicate` can be thought of as *evaluating* a binary relation, where the relation definition and one of the arguments are both established as a part of the Predicate declaration. 
> "A binary relation encodes the common concept of relation: an element x is related to an element y, if and only if the pair (x, y) belongs to the set of ordered pairs that defines the binary relation."
>
> -Wikipedia
 
Some common examples of binary relations are *Greater Than*, *Less Than*, *Equals*, *Divides Evenly*, etc.

```java
Predicate<Integer> greatherThanZero = x -> x > 0;
```
In this example the _relation_ is *"Greater Than"*, and `y` is 0. When we supply an integer `x` to the `Predicate`, it tests whether the ordered pair *(x, 0)* satisfies the relation, returning true if so, and false if not.

##### Figure 5.1

|**x** |**y**  | relation | evaluation |
|:----:|:-----:|:--------:|:----------:|
| _-2_ | **0** | x > y    | _false_    |
| _-1_ | **0** | x > y    | _false_    |
| _0_  | **0** | x > y    | _false_    |
| _1_  | **0** | x > y    | _true_     |
| _2_  | **0** | x > y    | _true_     |

### 5.1 Primitive Predicates

Once again there are versions of the `Predicate` interface for commonly used primitive types `double`, `int`, and `long`.

##### Figure 5.2

|            | **boolean**         |
|:----------:|:-------------------:|
| **int**    | _IntPredicate_      |
| **long**   | _LongPredicate_     |
| **double** | _DoublePredicate_   |

### 5.2 Two-Arity Predicates `(T, U) -> boolean`

The two-arity version of `Predicate` is `BiPredicate`. It is parameterized by the types of each argument `T` and `U` separately.

```java
public interface BiPredicate<T, U> { … }
```

It has the following unimplemented method signature.

```java
boolean test(T t, U u);
```

### 5.3 Primitive Two-Arity Predicates

There are no included functional interface for primitive specializations of `BiPredicate`.

### 5.4 Predicate Reference Examples

The `Stream` interface, and it's implementation in `ReferencePipeline` use `Predicate` for filtering. Filtering a `Collection` is probably the most common use of `Predicate`.

*java/util/stream/ReferencePipeline.java* _160_
```java
/**
 * Returns a stream consisting of the elements of this stream that match
 * the given predicate.
 *
 * <p>This is an <a href="package-summary.html#StreamOps">intermediate
 * operation</a>.
 *
 * @param predicate a <a href="package-summary.html#NonInterference">non-interfering</a>,
 *                  <a href="package-summary.html#Statelessness">stateless</a>
 *                  predicate to apply to each element to determine if it
 *                  should be included
 * @return the new stream
 */
@Override
public final Stream<P_OUT> filter(Predicate<? super P_OUT> predicate) {
    Objects.requireNonNull(predicate);
    return new StatelessOp<P_OUT, P_OUT>(this, StreamShape.REFERENCE,
                                 StreamOpFlag.NOT_SIZED) {
        @Override
        Sink<P_OUT> opWrapSink(int flags, Sink<P_OUT> sink) {
            return new Sink.ChainedReference<P_OUT, P_OUT>(sink) {
                @Override
                public void begin(long size) {
                    downstream.begin(-1);
                }

                @Override
                public void accept(P_OUT u) {
                    if (predicate.test(u))
                        downstream.accept(u);
                }
            };
        }
    };
}
```

The following are useful reference examples either from the core Java library, or from industry standard libraries. They can give you a good starting point into understanding which scenarios `Predicate` might be useful.

The Pattern class allows you convert a `Pattern` into a `Predicate` which can be used for `String` matching.

*java/util/regex/Pattern.java* _5757_
```java
/**
 * Creates a predicate which can be used to match a string.
 *
 * @return  The predicate which can be used for matching on a string
 * @since   1.8
 */
public Predicate<String> asPredicate() {
    return s -> matcher(s).find();
}
```


---

## 6.0 Operator

Operator interfaces are special cases of Function that receive and return the same value type. 

### 6.1 UnaryOperator `T -> T`

The `UnaryOperator` interface receives a single argument and returns a value of the same type. As such, it is the only functional interfaces to directly extend `Function`. (*There are semantic reasons why `Consumer` and `Supplier` do not extend `Function`)*

```java
public interface UnaryOperator<T> extends Function<T, T>
```

Because it extends `Function`, `UnaryOperator` inherits the following unimplemented method signature.

```java
T apply(T t);
```

To demonstrate the fact that `UnaryOperator` is simply a QOL functional interface, does not provide additional functionality on top of `Function`, take a look at the two examples below. Anywhere `UnaryOperator` is used, `Function` would work just as well, albeit slightly more verbose declaratively.

```java
UnaryOperator<Person> unaryOperator =  
        (person) -> { person.name = "New Name"; return person; };
```

The only difference when using `Function` is that the argument and return types must be specified explicitly.

```java
Function<Person, Person> function = 
        (person) -> { person.name = "New Name"; return person; };
```

### 6.2 Primitive UnaryOperator

Again, since a primitive type can’t be a generic type argument, there are versions of the `UnaryOperator` interface for commonly used primitive types `double`, `int`, and `long`.

##### Figure 6.1

| **int**               | **long**               | **double**               |
|:---------------------:|:----------------------:|:------------------------:|
| _IntUnaryOperator_    | _LongUnaryOperator_    | _DoubleUnaryOperator_    |


### 6.3 BinaryOperator `(T, T) -> T`

The `BinaryOperator` interface receives *two* arguments and returns a value of the same type. Similar to `UnaryOperator` is an extension of another functional interface, in this case `BiFunction`.

```java
public interface BinaryOperator<T> extends BiFunction<T,T,T> { ... }
```

Because it extends `BiFunction`, `BinaryOperator` inherits the following unimplemented method signature.

```java
T apply(T t, T t2);
```

### 6.4 Primitive BinaryOperator

Again, since a primitive type can’t be a generic type argument, there are versions of the `BinaryOperator` interface for commonly used primitive types `double`, `int`, and `long`.

##### Figure 6.2

| **int**               | **long**               | **double**               |
|:---------------------:|:----------------------:|:------------------------:|
| _IntBinaryOperator_   | _LongBinaryOperator_   | _DoubleBinaryOperator_   |

### 6.5 Operator Reference Examples

The `List` interface, and its various implementations use `UnaryOperator` for replacing each element in the `List` with an element of the same type, effectively *transforming* the `List`.

*java/util/List.java* _160_
```java
/**
 * Replaces each element of this list with the result of applying the
 * operator to that element.  Errors or runtime exceptions thrown by
 * the operator are relayed to the caller.
 *
 * @implSpec
 * The default implementation is equivalent to, for this {@code list}:
 * <pre>{@code
 *     final ListIterator<E> li = list.listIterator();
 *     while (li.hasNext()) {
 *         li.set(operator.apply(li.next()));
 *     }
 * }</pre>
 *
 * If the list's list-iterator does not support the {@code set} operation
 * then an {@code UnsupportedOperationException} will be thrown when
 * replacing the first element.
 *
 * @param operator the operator to apply to each element
 * @throws UnsupportedOperationException if this list is unmodifiable.
 *         Implementations may throw this exception if an element
 *         cannot be replaced or if, in general, modification is not
 *         supported
 * @throws NullPointerException if the specified operator is null or
 *         if the operator result is a null value and this list does
 *         not permit null elements
 *         (<a href="Collection.html#optional-restrictions">optional</a>)
 * @since 1.8
 */
default void replaceAll(UnaryOperator<E> operator) {
    Objects.requireNonNull(operator);
    final ListIterator<E> li = this.listIterator();
    while (li.hasNext()) {
        li.set(operator.apply(li.next()));
    }
}
```

The `Stream` interface has a static method for generating an infinite `Stream` by iteratively applying a `UnaryOperator` to the result of the previous application.

*java/util/stream/Stream.java* _1020_
```java
/**
 * Returns an infinite sequential ordered {@code Stream} produced by iterative
 * application of a function {@code f} to an initial element {@code seed},
 * producing a {@code Stream} consisting of {@code seed}, {@code f(seed)},
 * {@code f(f(seed))}, etc.
 *
 * <p>The first element (position {@code 0}) in the {@code Stream} will be
 * the provided {@code seed}.  For {@code n > 0}, the element at position
 * {@code n}, will be the result of applying the function {@code f} to the
 * element at position {@code n - 1}.
 *
 * @param <T> the type of stream elements
 * @param seed the initial element
 * @param f a function to be applied to to the previous element to produce
 *          a new element
 * @return a new sequential {@code Stream}
 */
public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f) {
    Objects.requireNonNull(f);
    final Iterator<T> iterator = new Iterator<T>() {
        @SuppressWarnings("unchecked")
        T t = (T) Streams.NONE;

        @Override
        public boolean hasNext() {
            return true;
        }

        @Override
        public T next() {
            return t = (t == Streams.NONE) ? seed : f.apply(t);
        }
    };
    return StreamSupport.stream(Spliterators.spliteratorUnknownSize(
            iterator,
            Spliterator.ORDERED | Spliterator.IMMUTABLE), false);
}
```

---

## 7.0 Bonus Goodies

### 7.1 1-arity JDK Provided Functional Interface Table

Below is a full matrix of JDK supplied 1-arity functional interfaces by param and return type.

##### Figure 7.1

|             | **double**             | **int**               | **long**               | **boolean**       | **float** | **char** | **byte** | **generic**        |     **void**     | 
|:-----------:|:----------------------:|:---------------------:|:----------------------:|:-----------------:|:---------:|:--------:|:--------:|:------------------:|:----------------:| 
| **double**  | _DoubleUnaryOperator_  | _DoubleToIntFunction_ | _DoubleToLongFunction_ | _DoublePredicate_ | **X**     | **X**    | **X**    | _DoubleFunction_   | _DoubleConsumer_ | 
| **int**     | _IntToDoubleFunction_  | _IntUnaryOperator_    | _IntToLongFunction_    | _IntPredicate_    | **X**     | **X**    | **X**    | _IntFunction_      | _IntConsumer_    | 
| **long**    | _LongToDoubleFunction_ | _LongToIntFunction_   | _LongUnaryOperator_    | _LongPredicate_   | **X**     | **X**    | **X**    | _LongFunction_     | _LongConsumer_   | 
| **boolean** | **X**                  | **X**                 | **X**                  | **X**             | **X**     | **X**    | **X**    | **X**              | **X**            | 
| **float**   | **X**                  | **X**                 | **X**                  | **X**             | **X**     | **X**    | **X**    | **X**              | **X**            | 
| **char**    | **X**                  | **X**                 | **X**                  | **X**             | **X**     | **X**    | **X**    | **X**              | **X**            | 
| **byte**    | **X**                  | **X**                 | **X**                  | **X**             | **X**     | **X**    | **X**    | **X**              | **X**            | 
| **generic** | _ToDoubleFunction_     | _ToIntFunction_       | _ToLongFunction_       | _Predicate_       | **X**     | **X**    | **X**    | _Function_         | _Consumer_       | 
| **void**    | _DoubleSupplier_       | _IntSupplier_         | _LongSupplier_         | _BooleanSupplier_ | **X**     | **X**    | **X**    | _Supplier_         | _Runnable_     |

### 7.2 Legacy Functional Interfaces

As of Java 8, certain legacy interfaces that fit the requirement for a functional interface have had the `@FunctionalInterface` annotation added, and can be implemented by a lambda expression. The following are just a select few examples.

*Runnable:* `void -> T`

```java
Thread t1 = new Thread(() -> System.out.println("Running Task"));

/// above lambda can also be replaced with method reference...

Thread t1 = new Thread(() -> System.out.println("Running Task"));
```

*Comparator:* `(T, T) -> int`

```java
Comparator<String> stringLengthComparator = (s1, s2) -> s1.length() - s2.length();

/// above lambda can also be replaced with method reference...

Comparator<String> stringLengthComparator = Comparator.comparingInt(String::length);
```

*Callable:* `void -> void`

```java
Integer result = Executors.newSingleThreadExecutor().submit(() -> doExpensiveCalculation()).get();
```

___
