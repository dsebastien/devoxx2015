# Java 8 Completable Future

## Links
* to check: ForkJoinPool

## Asynchronous

multithreaded execution on multiple cores vs real async

Difference with the synchronous multithreaded model
* the async engine decides to switch from one context to another
* ...

Pattern:
* data ... forEach(lambda expression)
* callback or task: lambda expression
* when the result is available then ...

## History
* Runnable
  * mute & blind
* Callable
  * can return a value
  * can throw exceptions
* ExecutorService (pool of threads)
  * get back a Future object
    * to get the result
      * Future<String> = executorService.submit(task)
      * List<User> users = future.get(); // blocking!
      * users.forEach(...)
  * Future acts as a bridge between multiple execution threads

## CompletableFuture
Brings a solution to chained asynchronous & multithreaded tasks.

The Jersey way to create an async call:
```
@Path("/resource")
public class AsyncResource {
    @Inject private Executor executor

    @GET
    public void asyncGet(@Suspended final AsyncResponse asyncResponse){
        executor.execute(() -> {
            String result = longOperation(); // executed in another thread
            asyncResponse.resume(result);
        });
        ...
    }
    ...
}
```

How to test the code above? No easy way given that we are in an asynchronous world.

We could mock AsyncResponse or even mock the result, then verify the correct interaction: `Mockito.verify(mockAsyncResponse).resume(result);`

But:
* we must also verify this once the run() method has been called
* we must take into account the multithreaded aspect...
  * the reads/writes on the mock should be 'visible'

This is where the `CompletionStage` interface comes to the rescue:

```
CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
    String result = longOperation();
    asyncResponse.resume(result);
}, executor);
```

On the object we can call:
```
...
```

Be careful of visibility issues:
* simpler to run everything in the same thread
* create, train and ...
* ...

Two elements in the API:
* CompletionStage interface
* an implementing class: CompletableFuture

The interface depends on CompletableFuture...
...

## CompletionStage
A model for a task:
* that performs an action and may return a value when another completion stage completes
* that may trigger other tasks


## CompletableFuture
A class implementing both Future and CompletionStage.

It has a state:
* the task may be running
* the task may have completed normally
* the task may have completed exceptionally

## Methods from Future
* cancel(mayInterruptIfRunning)
* isCanceled
* isDone
* get // may throw
* get(timeout) // may throw

## More from CompletableFuture
* join(): same as the get but does not throw checked exception
* getNow(V valueIfAbsent): returns immediately
* complete(V value): sets the returned value is not returned
* obtrudeValue: resets the returned value
* completeExceptionally: sets an exception
* obtrudeException: resets with an exception

## Creation
```
public static <U> CompletableFuture<U> completedFuture(U value);

runAsync...
supplyAsync...
```

## Build CompletionStage chains
A CompletionStage is a step in a chain
* it can be triggered by a previous CompletionStage
* it can trigger another CompletionStage
* it can be executed in a given Executor

A Task:
* can be a Function
* can be a Consumer
* can be a Runnable

Operations supported:
* chaining (1 - 1)
* composing (1 - 1)
* combining, waiting for both results (2 - 1)
* combining, triggered on the first available result (2 - 1)

The above gives 36 methods (but there are many more :p).

In what thread can it be executed?
* in the same executor as the caller
* in a new executor, passed as a parameter
* asynchronously ...

## CompletionStage patterns

1 - 1 patterns
* thenApply
* thenRunAsync: will be ran asynchronously
* thenComposeAsync: composition

2 - 1 patterns
* thenCombineAsync
* thenAcceptBoth
* runAfterBothAsync: will wait for the two CompletionStage
* applyToEither: waiting for the first result to be available
* acceptEitherAsync
* runAfterEitherAsync

## Back to the example

```
String result = Mockito.mock(String.class);
AsyncResponse response = Mockito.mock(AsyncResponse.class);

Runnable train = () -> Mockito.doReturn(result).when(response).longOperation();
...
```

```
ÈxecutorService executor = Executors.newSingleThreadExecutor();

AsyncResource asyncResource = new AsyncResource();
asyncResource.setExecutorService(executor);

CompletableFuture
    .runAsync(train, executor)
    .thenRun(callAndVerify)
    .get(10, TimeUnit.SECONDS);
```

## Another example
```
CompletableFuture.supplyAsync(
    () -> readPage("google.com")
).thenApply(page -> linkParser.getLinks(page))
.thenAccept(
    links -> displayPanel.display(links) // in the right thread
    executor // to ensure execution in the correct thread
);
```

```
Executor executor ...
```

## Async and CDI
CDI 2.0 will support async events:

```
@Inject
Event<String> event;

CompletionStage<Object> cs = event.fireAsync("some event"); // returns a CompletionStage object!
cs.whenComplete(...); // handle the exceptions
...
```

## CompletionStage - last patterns
* allOf
* anyOf

## Exception handling
A CompletableFuture can depend on:
* one CompletableFuture
* two CompletableFuture
* N CompletableFuture

Suppose that we have a chain of CompletableFuture

If an error occurs, calling `isCompletedExceptionally()` returns true and the call to `get()` throws an Exception

We can create an intermediate CompletableFuture with the `exceptionally()` method and if there is an exception, we can handle the exception (if possible) and avoid letting the exception going further down the chain.

## Last example

```
CompletableFuture<String> closing = new CompletableFuture<String>();
Stream<String> manyStrings = Stream.of("one", "two", "three");

CompletableFuture<String> reduced = manyStrings.parallel().onClose(() -> { // can run in parallel
    closing.complete("");
    .map(CompletableFuture::completedFuture)
    .filter(cf -> cf.get().length() < 20)
    .reduce(
        closing, // special completable future that will be used as the seed of the operation
        (cf1, cf2) -> cf1.thenCombine(cf2, binaryOperator) // concatenation
    )
});

manyStrings.close(); // will execute closing.complete and will trigger the chain of operations
```

## Conclusion
* rich but complex API
* complexity coming from the interface (large)
* built on lambdas
* gives fine control over threads
* handles chaining & composition
* provides a clean way of handling exceptions
