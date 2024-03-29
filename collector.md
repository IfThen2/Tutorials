<h1 style="text-align: center;">Java 8 Collector Guide</h1>

![Collector Title](src/main/resources/collector/jason-leung-D4YrzSwyIEc-unsplash.jpg "Collector Title")

---

<h2 style="text-align: center;">Collector Overview:</h2>

`Collector` represents a special *mutable* reduction operation. Elements are incorporated by
updating the state of a mutable container rather than by replacing the intermediate result. This is
desirable behavior when we want to reduce a Stream into some sort of `Collection`. It would be very
inefficient to create a new `Collection` Object during every step of the reduction (*as is typical
in reduction operations*), so we can use `Collector` to avoid that.

We'll dive deeper into the finer points of *collection* vs *reduction* in a separate tutorial. For
now, let's take a look at the pieces that make up a `Collector`.

<h3 style="text-align: center;">Container Supplier</h3>

<p align="center">
  <img src="src/main/resources/collector/supplier.svg" alt="Container Supplier">
</p>

The container supplier is responsible for creating a new mutable container for the result. It has
the following abstract method signature:

```java
Supplier<A> supplier();
```

<h2 style="text-align: center;">Accumulator</h2>

<p align="center">
  <img src="src/main/resources/collector/accumulator.svg" alt="Accumulator">
</p>

The accumulator incorporates data elements into the result container. It has the following abstract
method signature:

```java
BiConsumer<A, T> accumulator();
```

<h2 style="text-align: center;">Combiner</h2>

<p align="center">
  <img src="src/main/resources/collector/combiner.svg" alt="Combiner">
</p>

The combiner is used (*in the `Stream` framework*) during parallel execution. Separate process
separate sections of the `Stream`, accumulating their partial result into a mutable container. Those
containers eventually need to be combined into one single result, hence the combiner. It has the
following abstract method signature:

```java
BinaryOperator<A> combiner();
```

<h2 style="text-align: center;">Finisher</h2>

<p align="center">
  <img src="src/main/resources/collector/finisher.svg" alt="Finisher">
</p>

Performs optional final transformation. Collectors may set (*and the majority do*)
the `IDENTITY_TRANSFORM` characteristic, in which case the finishing transformation is an identity
function with an unchecked cast from `A` to `R`. It has the following abstract method signature:

```java
Function<A, R> finisher();
```

---

<h2 style="text-align: center;">An Example Visualized</h2>

Let's try to visualize an example `Stream` collection process to help understand the different
components. Also, be sure to note the differences between serial and parallel execution.

### Serial Collection:

![Serial Collection](src/main/resources/collector/serialcollection.svg "Serial Collection")

### Parallel Collection:

![Parallel Collection](src/main/resources/collector/parallelcollection.svg "Parallel Collection")

---

<h2 style="text-align: center;">What Are The Rules?</h2>

> To ensure that sequential and parallel executions produce equivalent results, the collector functions must satisfy an identity and an associativity constraints.
> -- <cite>JavaDoc</cite>

Essentially, there are two things that must hold true in order for a Collector to perform
equivalently during parallel and sequential execution.

### The Identity Constraint

<p align="center">
  <img src="src/main/resources/collector/identityconstraint.svg" alt="The Identity Constraint">
</p>

> The identity constraint says that for any partially accumulated result, combining it with an empty result container must produce an equivalent result. That is, for a partially accumulated result a that is the result of any series of accumulator and combiner invocations, a must be equivalent to
> ```java
> combiner.apply(a, supplier.get())
> ```
> -- <cite>JavaDoc</cite>

Essentially, combining a partial result with an empty result should be the same as passing the
partial result through the identity function. When combining two result containers, only the
specific contents of the containers should affect the result.

### The Associativity Constraint

<p align="center">
  <img src="src/main/resources/collector/associativityconstraint.svg" alt="The Associativity Constraint">
</p>

> The associativity constraint says that splitting the computation must produce an equivalent result. That is, for any input elements t1 and t2, the results r1 and r2 in the computation below must be equivalent:
> ```java
> A a1 = supplier.get();
> accumulator.accept(a1, t1);-
> accumulator.accept(a1, t2);
> R r1 = finisher.apply(a1);  // result without splitting
>
> A a2 = supplier.get();
> accumulator.accept(a2, t1);
> A a3 = supplier.get();
> accumulator.accept(a3, t2);
> R r2 = finisher.apply(combiner.apply(a2, a3));  // result with splitting
> ```
> -- <cite>JavaDoc</cite>

The associativity constraint is a little more straight-forward. A Collector is considered
associative if splitting (and thus processing the elements in a different order) produces the same
result.

I think most are familiar with the concept of associativity in algebra:

![Algebra Associativity](src/main/resources/collector/associativityalgebra.svg "Algebra Associativity")

If you stretch your mind a little bit you can look at associativity in collection the same way.

![Associativity Metaphor](src/main/resources/collector/associativitymathmetaphor.svg "Associativity Metaphor")

---

<h2 style="text-align: center;">A Collection of Collectors</h2>

Now that we have a feel for the different components of a `Collector`, let's take a look at just a
few of the JDK supplied Collectors.

### JDK Convenience Collectors

![JDK Collectors](src/main/resources/collector/jdkcollectors.svg "JDK Collectors")

<p align="center">
  <img src="src/main/resources/collector/jdkcollectornotes.svg" alt="JDK Collectors Notes">
</p>

<h2 style="text-align: center;">A Special Case: Collecting into Maps.</h2>

In order to collect into a `Map`, we need to upgrade our accumulator into a higher order function,
composed of three other functions. These "sub" functions are:

<h3 style="text-align: center;">Key Mapper</h3>

<p align="center">
  <img src="src/main/resources/collector/keymapper.svg" alt="Key Mapper">
</p>

The key mapper transforms each element from the stream into a key for the `Map` being collected
into.

<h3 style="text-align: center;">Value Mapper</h3>

<p align="center">
  <img src="src/main/resources/collector/valuemapper.svg" alt="Value Mapper">
</p>

The value mapper transforms each element from the stream into a value for the `Map` being collected
into.

<h3 style="text-align: center;">Merger</h3>

<p align="center">
  <img src="src/main/resources/collector/merger.svg" alt="Merger">
</p>

Any collisions (when two elements produce the same key) are handled by the merger. Many of the
predefined `Map` collectors just throw an `Exception` unconditionally, but you can easily supply
your own merge function if the desired behavior is more complex.

<h3 style="text-align: center;">All Composed Together</h3>

<p align="center">
  <img src="src/main/resources/collector/tomapcollector.svg" alt="To Map Collector">
</p>

<h2 style="text-align: center;">Build Your Own</h2>

What if none of the supplied Collectors meet our needs? In that case, implementing our own should be
no problem! Let's create a `Collector` similar to `Collectors.toList`, but that applies a finishing
step of copying the mutable result container into an `ImmutableList`.

![Custom Collector](src/main/resources/collector/customcollectortable.svg "JDK Collectors")

```java
public class ImmutableListCollector<T> implements Collector<T, List<T>, ImmutableList<T>> {

    @Override
    public Supplier<List<T>> supplier() {
        return ArrayList::new;
    }

    @Override
    public BiConsumer<List<T>, T> accumulator() {
        return List::add;
    }

    @Override
    public BinaryOperator<List<T>> combiner() {
        return (l1, l2) -> {
            l1.addAll(l2);
            return l1;
        };
    }

    @Override
    public Function<List<T>, ImmutableList<T>> finisher() {
        return ImmutableList::copyOf;
    }

    @Override
    public Set<Characteristics> characteristics() {
        return Collections.emptySet();
    }

    public static <T> ImmutableListCollector<T> toImmutableList() {
        return new ImmutableListCollector<>();
    }
}
```

### Try it Yourself!

Here is a sample Stream collection using a simple `Collector` implementation. Play around with both
of them, and run it to see the results!

<iframe frameborder="0" width="100%" height="500px" src="https://replit.com/@IfThen2/Java8StreamCollection?embed=true&v=1"></iframe>

---

<h2 style="text-align: center;">Resources / Further Reading</h2>

* https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.html

* https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.Characteristics.html

* https://apprize.best/javascript/lambda/6.html

* https://www.baeldung.com/java-8-collectors

* https://dkeenan.com/Lambda/

* https://www.amazon.com/Mock-Mocking-Bird-Including-Combinatory-ebook/dp/B00A1P096Y/ref=tmm_kin_swatch_0?_encoding=UTF8&qid=&sr=

---

<h2 style="text-align: center;">Hit Me Up</h2>

<p align="center">
  <img src="src/main/resources/collector/sociallogos.svg" alt="Social Media Logos">
</p>

- Twitter -> https://twitter.com/TheJavaImposter
- Linkedin -> https://www.linkedin.com/in/christopherjcooke/
- Medium -> https://medium.com/@christopher.jamescooke

<p align="center">
  <img src="src/main/resources/collector/javaimposterlogo.svg" alt="Java Imposter Logo">
</p>



