## Functional Interfaces

# Table of contents

- [Functional Interfaces](#functional-interfaces)
- [1.0 Overview:](#10-overview)
- [2.0 Function](#20-function)
  - [2.1 Primitive Functions](#21-primitive-functions)
  - [1.2 Two-Arity Functions](#12-two-arity-functions)
- [2.0 Supplier](#20-supplier)
  - [2.1 Primitive Suppliers](#21-primitive-suppliers)
- [3.0 Consumer](#30-consumer)
  - [3.1 Primitive Consumers](#31-primitive-consumers)
  - [3.2 Two-Arity Consumers](#32-two-arity-consumers)
  - [3.3 Primitive Two-Arity Consumers](#33-primitive-two-arity-consumers)
- [4.0 Predicate](#40-predicate)
  - [4.1 Primitive Predicates](#41-primitive-predicates)
  - [4.2 Two-Arity Predicates](#42-two-arity-predicates)
- [5.0 Operator](#50-operator)
  - [5.1 UnaryOperator](#51-unaryoperator)
  - [5.2 Primitive UnaryOperator](#52-primitive-unaryoperator)
  - [5.3 BinaryOperator](#53-binaryoperator)
  - [5.4 Primitive BinaryOperator](#54-primitive-binaryoperator)

---

## 1.0 Overview:


Any `interface` with a SAM (Single abstract method) is a _functional interface._

```java
@FunctionalInterface
public interface MyFunctionalInterface {
    public void doSomething();
}
```
:::tip Tip
The `@FunctionalInterface` annotation is preferred but not required. It can be helpful to add, however, as it both clarifies intent and also allows the compiler to generate errors when the requirements for a functional interface are not met.

:::
Methods signatures with `default` and `static` modifiers are not abstract, and as such a functional interface can have multiple `default` or `static` methods.


```java
@FunctionalInterface
public interface MyFunctionalInterface {
    public void doSomething();

    public default void print(String text) {
        System.out.println(text);
    }

    public static void print(String text, PrintWriter writer) throws IOException {
        writer.write(text);
    }
}
```

Functional interfaces can be implemented with a concrete class.

```java
public class SayHello implements MyFunctionalInterface
 {
    @Override
    public void doSomething() {
         System.out.println("Hello World");
    }
}
```

They can also be implemented by a _lambda_ expression.

```java
MyFunctionalInterface helloWorld = () -> {
    System.out.println("Hello World");
}
```

There are several functional interfaces provided by the JDK in the `java.util.function` package, but you can also create your own to suit your more specific needs.


---


## 2.0 Function

The simplest and most general functional Interface is the `Function` interface itself which is parameterized by the types of its argument `T` and a return value `R`.

```java
public interface Function<T, R> { … }
```

The single unimplemented method for `Function` has the following signature.

```java
R apply(T t);
```

### 2.1 Primitive Functions


Since a primitive type can’t be a generic type argument, there are versions of the `Function` interface for all possible combinations of argument and return types for the most commonly used data types `double`, `int`, and `long`.

##### Figure 1.1
|            | **int**               | **long**               | **double**               | **generic**      |      
|   :---:    |         :---:         |        :---:           |        :---:             |    :---:         |
| **int**    | **X**                 | _IntToLongFunction_    | _IntToDoubleFunction_    | _IntFunction_    |        
| **long**   | _LongToIntFunction_   | **X**                  | _LongToDoubleFunction_   | _LongFunction_   |        
| **double** | _DoubleToIntFunction_ | _DoubleToLongFunction_ | **X**                    | _DoubleFunction_ |      
| **generic**| _ToIntFunction_       | _ToLongFunction_       | _ToDoubleFunction_       | **X**            |            



### 1.2 Two-Arity Functions


In some situations we may want to define a function with two arguments. Luckily, Java provides functional interfaces for such scenarios as a part of the JDK. The generic two-arity version of `Function` is `BiFunction`. It is parameterized by the types of each argument `T` and `U` separately.

```java
public interface BiFunction<T, U, R> { … }
```

The single unimplemented method for `BiFunction` has the following signature.

```java
R apply(T t, U u);
```


### 1.3 Primitive Two-Arity Functions
There are versions of the `BiFunction` interface for the most used primitive types `double`, `int`, and `long`. Note that there are no two-arity specializations of `BiFunction` that accept primitive argument types_ (only primitive return values)._ If such a need arises, we must create those functional interfaces ourselves.

##### Figure 1.2
|             | int               | long               | double               |
|:-----------:|:-----------------:|:------------------:|:--------------------:|
| **generic** | _ToIntBiFunction_ | _ToLongBiFunction_ | _ToDoubleBiFunction_ |






---






## 2.0 Supplier


The Supplier interface is a specialization of `Function` which takes no arguments but still returns a value. It is parameterized only by the return type `T`.

```java
public interface Supplier<T> { … }
```

The single unimplemented method for `Supplier` has the following signature.

```java
T get();
```

By far the most common use cases are lazy value generation and sequence generation. 

The `Supplier` interface can also be thought of as a factory interface. Here is an example implementation which returns a new `Integer` instance with a random value between 0 and 1000.

```java
Supplier<Integer> randomSupplier = () -> new Integer((int) (Math.random() * 1000D));
```

### 2.1 Primitive Suppliers


Similar to `Function`, there are versions of the `Supplier` interface for the most used primitive types `double`, `int`, and `long`. Breaking from the established pattern, `Supplier` has one additional primitive specialization for the `boolean` data type.

##### Figure 2.2
|          | **int**         | **long**         | **double**         | **boolean**         |
|:--------:|:---------------:|:----------------:|:------------------:|:-------------------:|
| **void** | _IntSupplier_   | _LongSupplier_   | _DoubleSupplier_   | _BooleanSupplier_   |



### 3.2 Two-Arity Suppliers
There are no included functional interfaces for a two-arity `Supplier`.


### 3.3 Primitive Two-Arity Suppliers
Since there is no generic two-arity `Supplier`, it stands to reason there are also no primitive specializations.






---






## 3.0 Consumer


The `Consumer` interface accepts a generified argument and returns nothing (`void`). It can be thought of as the opposite of `Supplier`. It is parameterized only by the type of it's argument `T`.

```java
public interface Consumer<T> { … }
```

The single unimplemented method for `Consumer` has the following signature.

```java
void accept(T t);
```


### 3.1 Primitive Consumers


Once again there are versions of the `Consumer` interface for the most used primitive types `double`, `int`, and `long`.

##### Figure 3.1
|            | **void**           |
|:----------:|:------------------:|
| **int**    | _IntConsumer_      |
| **long**   | _LongConsumer_     |
| **double** | _DoubleConsumer_   |


### 3.2 Two-Arity Consumers


The generic two-arity version of `Consumer` is `BiConsumer`. The argument types do not need to match since `BiConsumer` parameterized by the types of each argument `T` and `U` separately.

```java
public interface BiConsumer<T, U> { … }
```

`BiConsumer` has the following **SAM** signature:
```java
void accept(T t, U u);
```

### 3.3 Primitive Two-Arity Consumers


Again there are versions of the `BiConsumer` interface for the most used primitive types `double`, `int`, and `long`. Note that only one of the arguments is generified, while the other is of the primitive type specified.

##### Figure 3.3
|            | **void**              |
|:----------:|:---------------------:|
| **int**    | _ObjIntConsumer_      |
| **long**   | _ObjLongConsumer_     |
| **double** | _ObjDoubleConsumer_   |






---






## 4.0 Predicate


In the traditional mathematical sense, a predicate is simply a function that receives a value and returns either _true_ or _false_ _(1 or 0)_. The `Predicate` functional interface is a specialization of `Function` that receives a generified value and returns a `boolean`. It is parameterized by its' argument type `T`.

```java
public interface Predicate<T> { … }
```
Predicate has the following unimplemented method.
```java
boolean test(T t);
```

### 4.1 Primitive Predicates
Once again there are versions of the `Predicate` interface for the most used primitive types `double`, `int`, and `long`.

##### Figure 4.0
|            | **boolean**         |
|:----------:|:-------------------:|
| **int**    | _IntPredicate_      |
| **long**   | _LongPredicate_     |
| **double** | _DoublePredicate_   |


### 4.2 Two-Arity Predicates
The two-arity version of `Predicate` is `BiPredicate`. It is parameterized by the types of each argument `T` and `U` separately.
 
```java
public interface BiPredicate<T, U> { … }
```

It has the following unimplemented method signature.

```java
boolean test(T t, U u);
```


### 3.3 Primitive Two-Arity Predicates
There are no included functional interface for primitive specializations of `BiPredicate`.






---






## 5.0 Operator


Operator interfaces are special cases of Function that receive and return the same value type. As such, they are the only functional interfaces to directly extend Function.

### 5.1 UnaryOperator


The `UnaryOperator` interface receives a single argument and returns a value of the same type. it extends the Java `Function` interface directly, because although all functional interfaces can be thought of as functions in the mathematical sense, `UnaryOperator` is the only one that is directly a specialization of the `Function` functional interface.

```java
public interface UnaryOperator<T> extends Function<T, T>
```

Because it extends `Function`, `UnaryOperator` inherits the following unimplemented method signature.

```java
T apply(T t);
```



To demonstrate the fact that `UnaryOperator` is simply a helpful functional interface, and is not required, take a look at the two examples below. Anywhere `UnaryOperator` is used, `Function` would work just as well, albeit slightly more verbose declaratively.

```java
UnaryOperator<Person> unaryOperator =  
        (person) -> { person.name = "New Name"; return person; };
```

The only difference when using `Function` is that the argument and return types must be specified explicitly.

```java
Function<Person, Person> function = 
        (person) -> { person.name = "New Name"; return person; };
```

### 5.2 Primitive UnaryOperator


Again, since a primitive type can’t be a generic type argument, there are versions of the `UnaryOperator` interface for the most used primitive types `double`, `int`, `long`.

##### Figure 5.2



### 5.3 BinaryOperator


The `BinaryOperator` interface receives two arguments and returns a value of the same type. 


### 5.4 Primitive BinaryOperator


Again, since a primitive type can’t be a generic type argument, there are versions of the `BinaryOperator` interface for the most used primitive types `double`, `int`, `long`.






---



## 6.0 Conclusion

Full matrix of JDK supplied functional interfaces by param and return type.



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
| **void**    | _DoubleSupplier_       | _IntSupplier_         | _LongSupplier_         | _BooleanSupplier_ | **X**     | **X**    | **X**    | _Supplier_         | **X**            |



