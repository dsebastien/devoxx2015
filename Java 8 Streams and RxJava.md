# Java 8 Strams and RxJava - Comparison: patterns & performance

## Links
* José Paumard: @JosePaumard
* JEP: http://openjdk.java.net/jeps/266

## Why Streams API?
All about data processing. Historically, only the collections API in Java.
Since 2014, many new APIs appeared.

Complexity & need rising over the years:
* larger data sets
* need for controlled response time
* need for higher level primitives

Solutions need to support:
* map, filter, reduce and other higher order functions
* accessing data anywhere
* parallel execution

## Java 8 Stream API
A Java 8 stream is an object that connects to a source.
* the stream does NOT hold any data
* implements the map/filter/reduce high order functions
* a stream does not modify the data it gets from the source

Basic examples:
```
List<String> list = Arrays.asList("one","two");
list.stream()
    .map(s -> s.toUpperCase()) // returns a stream
    .filter(s -> s.length() < 20) // returns a stream
    .max(Comparator.comparing(s -> s.length())) // triggers the calculations and returns an "Optional"
    .ifPresent(s -> System.out.println(s)) // check the Optional
```

We can use collectors:
```
List<Person> list = ... ;
Map<Integer, list.stream()
    .filter(person -> person.getAge() > 30)
    .collect(
        Collectors.groupingBy(
            Person::getAge, // key extractor
            Collectors.counting() // downstream collector
        )
    )
```

```
List<Person> people = Arrays.asList(p1,p2,p3);
Stream<Person> stream = people.stream();
...
```

Stream on String:
```
String crazy = "supercalifragilistic...";
IntStream letters = crazy.chars();
long count = letters.distinct.count();

letters.boxed().collect(
    Collectors.groupingBy(
        Function.identity(),
        Collectors.counting()
    )
)
```

Stream built on the lines of a file:
```
String book = "bidule";
Stream<String> lines = Files.lines(Paths.get(book)); // autocloseable
```

Stream built on the words of a String
```
String line = "Super fun way to split stuff";
Stream<String> words = Patterns.compile(" ").splitAsStream(line);
```

The pattern above is much more efficient than the old way...

Flatmap: mix both examples above:

```
Function<String, Stream<String>> splitToWords = line -> Pattern.compile(" ").splitAsStream(line);

Stream<String> lines = files.lines(path);
Stream<String> words = lines.flatMap(splitToWords);
```

The example above is memory-efficient: built on lazy reading of the file contents.

No Stream interface on the Map interface itself; there's a trick to have it: `map.entrySet().stream() ... `.

Example:
```
map.entrySet().stream()
    .sorted(
        Entry.<Integer, Long> comparingByValue().reversed()
    )
    .limit(3)
    .forEach(System.out::println);
```

## Connecting a Stream on a source
Connecting a Stream on a non-standard source is possible.

A Stream is built on 2 things:
* a Spliterator (comes from split and iterator)
* a ReferencePipeline (the implementation)

The Spliterator is meant to be overriden:
```
public interface Spliterator<T> {
    boolean tryAdvance(Consumer<? super T> action);
    Spliterator<T> trySplit(); // not needed for non-parallel operations
    long estimateSize(); // cannot know the size in advance: just an estimation of the size (can return 0)
    int characteristics(); // returns a constant
}
```

Example:
* suppose we have a Stream: [1, 2, 3, 4, ...]
* we want to regroup the elements [[1, 2, 3], [4, 5, 6`, ...]]

```
public class GroupingSpliterator<E> implements Spliterator<Stream<E>>{
    private final long grouping;
    private final Spliterator<E> spliterator;

    public long estimateSize(){
        return spliterator.estimateSize() / grouping;
    }

    public Spliterator<Stream<E>> trySplit(){
        ...
    }

    public boolean tryAdvanced(Consumer<? super Stream<E>> action){
        // should call action.accept() with the next element of the stream
        // and return true if more elements are to be consumed
        return true; // false when we are done
    }
}
```

Stream builder:
```
Stream.Builder<E> builder = Stream.build();
for(int i = 0; i < grouping; i++){
    spliterator.tryAdvance(element -> builder.add(element))){
        finished = true;
    }

    Stream<E> subStream = subBuilder.build();
    action.accept(subStream);
    return !finished;
}
...
```

Another example using a ring buffer:
...

## Spliterator on Spliterator
Merge multiple streams into one:
* [1, 2, 3, ...]
* [a, b, c, ...]

ZippingSpliterator

...

## Spliterator characteristics() method
Important for optimization (memory footprint & calculation)

```
public interface Spliterator<T> {
    ...
}
```

Bitmask-based.

```
people.stream()
    .sorted() // quicksort? Dependcs on the value of the SORTED bit; if 0; no sort required
    .collect(Collectors.toList());
```

## Characteristics can change
Each Stream object in a pipeline has its own characteristics.

## Stream API wrap-up
Java 8 Stream API is not just about implementing map, filter, reduce or building hashmaps.

...

## RxJava

### About
Open source API. Not to be seen as an alternative implementation of Java 8 Streams nor the Collection framework.

Implementation of the reactor pattern.

### API
The central class is the Observable class (10K LOC), quite complex (~100 static methods, ~150 non-static methods).

There is also the Observer interface used to observe an Observable.

Three methods:
```
public interface Observer<T> {
    public void onNext(T t);
    public void onCompleted();
    public void onError(Throwable e);
}
```

There is also the Subscription object

To create a subscription:
```
...
```

We can also just declare a callback:
```
Observable<T> observable = ...;
Observable<T> next = observable.doOnNext(System.out.println(...));
```

An Observable can be seen as a source of data.


### Creating an Observable
```
Observable<String> obs = Observable.just("one", "two");
List<String> strings = Arrays.asList("one", "two");
Observable<String> obs2 = Observable.from(strings);
```

Observable useful just for tests:
```
...
```

Series and time series:
```
Observable<Long> longs = Observable.range(1L, 100L);

...
```

The using() method:
```
public final static<T, Resource> Observable<T> using(
    final Func0<Resource> resourceFactory, // producer
    final Func1<Resource> Observable<T>> observableFactory, // factory
    ...
){ }
```

The using method:
* creates a resource that will provide the elements
* uses this resource to create the elements
* ...

...

Suppose we want to create an Observable on the lines of a text file
* need a BufferedReader
* need to wrap the lines one by one in Observables
* close the BufferedReader

```
Funct0<BufferedReader> resourceFactory = () -> new BufferedReader(new FileReader(new File(fileName)));

Action1<BufferedReader> disposeAction = br -> br.close();
```

...

Creating an Observable from an Iterator: `Observable.from(() -> iterator);`

The using method is made to build an Observable on a resource that needs to be "closed" at the end of the generation of the stream.

### Schedulers
Executors (pools of threads) allow the execution of Observable instances in specialized Thread pools (e.g., IO, Computation).

...

Factory Schedulers:

...

The Schedulers.from(executor) is useful to call observers in certain threads.

...

Example with a Timer:

```
Observable<Integer> timer = Observable.timer(1, TimeUnit.SECONDS);
...
```

Observables are run in their own schedulers (executors).

### Merging Observables
RxJava provides a collection of methods to merge observables together.

With many different, very interesting semantics.

RxJava uses marble diagrams to represent observables.

Some operators:
* amb: ambiguous (take one, drop the other)
* zip: combine
* combineLatest: generate an element each time one of the observables emits a value
* concat: emits O1 then O2, without mixing them stops on error
* ...

### Observables of Observables
...

### Hot and cold Observables
Functionality not present in the Java 8 Stream API.
A cold Observable does not generate values if there are no Observers.

A clock is an hot observable: it generates values even if there are no Observers.

Example:
```
Observable<Long> timer = Observable.interval(1, TimeUnit.SECONDS);
...
Observable<String> manyStrings =
    Observable.combineLatest(
        timer, Observable.just("one"),
            (i, s) -> i + " - " + s).forEach(System.out::println);
    );

Thread.sleept(5500);

Observable<String> moreManyStrings = ...
...
```

There is a `cache(int capacity) { ... }` method. Not great because of the memory footprint impact. A better alternative is to use a ConnectableObservable.

```
// does not count as a subscription
ConnectableObservable<Long> ...
...
```

It provides a way to create subscriptions to an Observable without making it hot directly. Calling the `connect()` method lets the subscriptions go through.

### Map, filter, reduce, ...
...

### Materializing
Materialize: special type of mapping:
...

### Selecting ranges
* first()
* last()
* skip()
* limit()
* ...

### Special filtering
Special functions:

...

### Reductions and collections
...

### Wrap-up on map/filter/reduce
All those methods return an Observable.

The result is thus wrapped in an Observable. Chaining operations is done with `flatMap()`. Wrapping and unwrapping has a cost...

### Repeating and retrying

```
repeat() { }            // Observable<T>
repeat(long times) { }  // Observable<T>
repeatWhen(
    Func1<Observable<Void>>, Observable<?>> notificationHandler
)
```

* on the onComplete of the source Observable, the notification handler is passed an Observable
* if the notification handler calls the onError or onComplete of this passed Observable, then repeatWhen calls the same on the ...

...

### Sampling
...

### Fast hot observables
Notion of back pressure: what happens if a hot observable emits values too fast?
...

### Backpressure methods
Way to slow down the emission of elements. It can act on the observing side

Strategies:
* buffering items
* skipping items (e.g., sampling)

Examples:
* temperature sensors
  * maybe we can skip some measures and ignore others: not necessarily leads to information loss
* Youtube example
  * graceful degradation of the quality: ...
  * not necessarily an issue

Analyze the source of data you're observing and just get what you need.

Acting at the Observable side might also be important

### Buffering
```
buffer(int size) { ... } // Observable<List<T>>
buffer(long timeSpan, timeUnit unit) { ... }
...

### Windowing
...

### Throttling
Throttling is a sampler on the beginning and end of a Window.

...
```

### Debouncing
Takes a delay and if multiple elements are emitted during that delay, only the first one is returned.

### Wrap-up on RxJava
* complex API
* many different concepts
* many methods
* allows to process data in chosen threads (useful for IO, computation and specialized threads)
* allows the synchronization of operations
  * on clocks
  * on application events
* works in pull mode, and also in push mode
* backpressure supports

## Getting the best of both worlds
If we have a Stream, we can easily build an Observable from it.
The other way around:
* we can build an Iterator on an Observable
* then build a Spliterator on an Iterator (must do it ourselves)

To implement an iterator:
* ...
* delete method is now a default method (Java 8)

The trick is that:
* an iterator ...
* ...

...

## Iterating on an Observable
How has it been done in the JDK?

...

## Comparison: patterns
Most of the time, the Java 8 Stream API is cleaner, simpler and there are factory methods for Collectors.

RxJava: flatMap calls, lack of factory methods

Java 8 can easily go parallel, which is a plus (parallel() method on a Stream).

## Comparison: Performances
Let us use JMH: standard tool for measuring code performance on the JVM (developed as an Open JDK tool).

JMH will be part of Java 9.

JMH is easy to set up using Maven.

Adds annotations that we can put on classes to benchmark.

Usage is easy:
```
mvn clean install
java -jar target\benchmark.jar
```

RxJava spends a lot of time opening observables, due to the flatMap patterns.

## Java 9

### About
Should come out in September 2016.
Apart from Jigsaw, Java 9 will also include an API to handle reactive streams.

A Stream:
* does not hold any data
* does not modify its data
* connects to a source of data: one source = one stream
* consumes the data from the source: "pull mode"

Let's bend the rules:
* connecting several streams to a single source?
* connecting several sources to a single stream?
* having a source that produces data whether or not a stream is connected to it

This will lead to the Reactive Stream API.
Will be part of a new package: java.util.concurrent.flow

Example:
```
public interface Publisher<T> {
    public ... subscribe(Subscriber<T> subscriber);
}

public interface Subscriber<T>{
    public void onSubscribe(Subscription subscription);
    public void onNext(T item);
    public void onComplete();
    public void onError(Throwable throwable);
}
...
```

Very similar to RxJava.

Having a source that produces data independently from its consumers implies to work in an asynchronous mode. The API is built on callbacks.

### Several streams per source
...

### Several sources for a stream
...

### Merging sources in push mode
...


### Backpressure
Several strategies:
* create a buffer (meh)
* synchronize on a clock or a gate that could be generated by the slow observer and sample, or windows or debounce, or ...
* try to slow down the source (need to have the hand on both the producer and the consumer)
* have several observers in parallel and then merge the results

### Status
New concept (at least in the JDK).
Brings new complexity but new opportunities for processing data.
Still in the works...
