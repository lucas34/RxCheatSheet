# RxCheatSheet

> **Observable:** Emit

> **Observer:** Listen


## Types of Observables

|               |     Observable   |  Flowable |   Maybe   |   Single   | Completable |
|---------------|------------------|-----------|-----------|------------|-------------|
| Items emitted |     A most N     | A most N  | At most 1 |  Exactly 1 |  Exactly 0  |
|  cardinality  |     (0...N)      |  (0...N)  |  (0..1)   |   (1..1)   |   (1..1)    |
| Backpressure  |        No        |    Yes    |    No     |     No     |     No      |

*Subject* is an `Observable` AND an `observer` at the same time. Not a type of `observable`.

## Observer

`onNext()` Called each time an item is emitted.<br />
`onCompleted()` Called when there is no more item.<br />
`onError()` Stream errored.<br />

When the sequence is terminated (no more item will be emited) either `onCompleted` or `onError` is called but not both.

`subscriber` is an implementation of `observer`.

## Subscriber

An `observable` will start emitting only when someone is listening and will stop when no one is listening anymore.
A sequence cannot be resumed after canceling.
`Observable<String>.create({ _ -> // Never called})`

Each of this following subscription will be wrapped with a `LambdaObserver` in RxJava.
It will also return a disposable to allow canceling.

`Observable<String>.subscribe(Consumer<String>)`
`Observable<String>.subscribe(Consumer<String>, Consumer<Error>)`
`Observable<String>.subscribe(Consumer<String>, Consumer<Error>)`
`Observable<String>.subscribe(Consumer<String>, Consumer<Error>, Action)`

If you pass an `observer` as argument, the method will return void.
To allow canceling, your `observer` has to implement `disposable` as well.
`class MyCancelableObserver implements Observer, Disposable { //code }`
`Observable<String>.subscribe(MyCancelableObserver)`

## Global error handler: RxJava and RxSwift (4.0+)

When the sequence errored, the exception will be deliver to the `observer` or if not possible to a global error handler.

Example for Java:

Case 1: You don't implement `onError`
`Observable<String>.subscribe(Consumer<String>)`
Exception will be wrapped with OnErrorNotImplementedException(Throwable).

Case 2: You rethrow the exception inside onError
If you are using the `LambdaObserver` and your `onError` throw and unchecked exception.
`Observable.subscribe(_ -> {}, error -> { throw new RuntimeException("Oups"); })`.
Exception will be wrapped with CompositeException(Throwable).

## Schedulers operators

- `subscribeOn()`: Change root upstream execution thread
- `observeOn()`: Change downstream execution thread

```swift
Observable.create { _ -> // Caller thread }
Observable.create { _ -> // BackgroundThread }.subscribeOn(BackgroundThread)
Observable.create { _ -> // Caller thread }.observeOn(BackgroundThread).map { _ -> //BackgroundThread }
```
