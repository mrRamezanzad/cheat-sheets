# RxJS
Is a library for reactive programming using Observables, to make it easier to compose asynchronous or callback-based code.

Pure JavaScript Example:

```js
document.addEventListener('click', () => console.log('Clicked!'));
```

Observable Way:

```js
import { fromEvent } from 'rxjs';

fromEvent(document, 'click').subscribe(() => console.log('Clicked!'));
```

## Essential Concepts

### Observable

represents the idea of an invokable collection of future values or events.

### Observer

is a collection of callbacks that knows how to listen to values delivered by the Observable.

### Subscription

represents the execution of an Observable, is primarily useful for cancelling the execution.

### Operators

are pure functions that enable a functional programming style of dealing with collections with operations like map, filter, concat, reduce, etc.

### Subject

is equivalent to an EventEmitter, and the only way of multicasting a value or event to multiple Observers.

### Schedulers

are centralized dispatchers to control concurrency, allowing us to coordinate when computation happens on e.g. setTimeout or requestAnimationFrame or others.

## Benefits

### Purity

What makes RxJS powerful is its ability to produce values using pure functions. That means your code is less prone to errors.

Normally you would create an impure function, where other pieces of your code can mess up your state.
e.g.1

```js
let count = 0;
document.addEventListener('click', () =>
  console.log(`Clicked ${++count} times`)
);
```

e.g.2

```js
import { fromEvent, scan } from 'rxjs';

fromEvent(document, 'click')
  .pipe(scan((count) => count + 1, 0))
  .subscribe((count) => console.log(`Clicked ${count} times`));
```

> The scan operator works just like reduce for arrays.

### Flow

RxJS has a whole range of operators that helps you control how the events flow through your observables.

This is how you would allow at most one click per second, with plain JavaScript:
e.g.1

```js
let count = 0;
let rate = 1000;
let lastClick = Date.now() - rate;
document.addEventListener('click', () => {
  if (Date.now() - lastClick >= rate) {
    console.log(`Clicked ${++count} times`);
    lastClick = Date.now();
  }
});
```

e.g.2

```js
import { fromEvent, throttleTime, scan } from 'rxjs';

fromEvent(document, 'click')
  .pipe(
    throttleTime(1000),
    scan((count) => count + 1, 0)
  )
  .subscribe((count) => console.log(`Clicked ${count} times`));
```

> Other flow control operators are filter, delay, debounceTime, take, takeUntil, distinct, distinctUntilChanged etc.

### Values
You can transform the values passed through your observables.

Here's how you can add the current mouse x position for every click, in plain JavaScript:
e.g.1

```js
let count = 0;
const rate = 1000;
let lastClick = Date.now() - rate;
document.addEventListener('click', (event) => {
  if (Date.now() - lastClick >= rate) {
    count += event.clientX;
    console.log(count);
    lastClick = Date.now();
  }
});
```

e.g.2

```js
import { fromEvent, throttleTime, map, scan } from 'rxjs';

fromEvent(document, 'click')
  .pipe(
    throttleTime(1000),
    map((event) => event.clientX),
    scan((count, clientX) => count + clientX, 0)
  )
  .subscribe((count) => console.log(count));
```

> Other value producing operators are pluck, pairwise, sample etc.
> 'subscribe' can get three methods as observer object, { next: fn, error: fn, complete: fn }

### Create

```js
create((obs) => {
    obs.next(1);

    setTimeout(() => obs.complete()), 5000};

    obs.next(2);

    obs.error('something went wrong');
    obs.next(3)
    )
```

### unsubscribe

```js
const subscription = create((obs) => obs.next(1);)
.subscribe({ next: console.log, error: console.error, complete: console.info })

setTimeout(() => subscription.unsubscribe(), 5000)
```

### interval
Creates an Observable that emits sequential numbers every specified interval of time, on a specified SchedulerLike.
`interval(period: number = 0, scheduler: SchedulerLike = asyncScheduler)`

## RxJS 6.0
Imports has changed:
```js
// old version
import 'rxjs/add/observable/fromPromise';

// new version
import {fromPromise} from 'rxjs';

// imports are mostly from 
// from 'rxjs' & from 'rxjs/operators'
```
> In versions > 7.0 all operators are alse moved to 'rxjs'. 

Renamed Operators:  
| old versions | new versions      |
|--------------|-------------------|
| do()         | tap()             |
| catch()      | catchError()      |
| switch()     | switchAll()       |
| finally()    | finalize()        |
| throw()      | throwError()      |

> Because they are reserved keywords in js.  

Since this version lots of things changed and codes may not work so a quick fix is:
`npm install --save rxjs-compat`

> Ensures Backward-Compatibility and old code shouldn't require any changes.

## Subject (~EventEmitter)
Subject is a special type of Observable that allows values to be multicasted to many Observers. While plain Observables are unicast (each subscribed Observer owns an independent execution of the Observable), Subjects are multicast.

Observable is passive e.g. wraps callback, event, etc. But 'Subject' is active and can be triggered from code by calling `next()`.

```js
const subject = new Subject();

subject.subscribe({
    next: console.log,
    error: console.err,
    complete: console.info,
});

subject.subscribe({
    next: console.log,
});

subject.next('A new data piece');
subject.complet();
```
> Subject works like Observable but your decide when to call it and what happens.

## filter() Operator
filters values during pipeline. It just needs to return true or false (value will be dropped).

```js
const observable = interval(1000);

observable
.filter((value) => value % 2 === 0) 
// we can pass `this` as second argument if we wanna use it in filter
.subscribe({
    next: console.log,
    error: console.error,
});
```

## debounceTime() & distinctUntilChanged() Operators

e.g. Here is how to prevent unchanged input value to send request to server
```js
const input = document.querySelector('input');
const observable = fromEvent(input, 'input');

observable
.pipe(map((event) => event.target.value))
.debounceTime(500)
.distintUntilChanged() 
// only passes value if value is different from last one
.subscribe({
    next: console.log,
    error: console.error,
});
```

## reduce() & scan() Operators
Like reduce in array methods here for observables which we know it will have multiple parts and we are only interested in it all together.

e.g.1
```js
const observable = of(1, 2, 3, 4, 5);

observable.
.pipe(
    reduce((total, currentValue) => total + currentValue, 0)
)
.subscribe({
    next: console.log
});
```

```
output:
 15
```

scan is like reduce but it also passes all values in between before total and it can be infinite and just for monitoring and not ending data.

e.g.2
```js
const observable = of(1, 2, 3, 4, 5);

observable.
.pipe(
    scan((total, currentValue) => total + currentValue, 0)
)
.subscribe({
    next: console.log
});
```

```
output:
 1
 3
 6
 10
 15
```

## pluck() Operator
Pluck means remove, pick of, pick, pull, pull off/out, extract, take.  
For extracting property from and object we can use `pluck` instead of `map` for shorter and concise syntax.

```js
const input = document.querySelector('input');
const observable = fromEvent(input, 'input');

observable
.pipe(pluck('target', 'value'))
.debounceTime(500)
.distintUntilChanged() 
.subscribe({
    next: console.log,
    error: console.error,
});
```
## mergeMap() Operator
Merge results of two operators from two separate observables.
Usecase is for when we have multiple data sources and one of them tells us when the change has happend.

e.g.
```js
const input1 = document.querySelector('#input1');
const input2 = document.querySelector('#input2');
const span = document.querySelector('span');

const obs1 = fromEvent(input1, 'input');
const obs2 = fromEvent(input2, 'input');

obs1.mergeMap(
    (event1) => {
        return obs2.pipe(map(
            (event2) => event1.target.value + ' ' + event2.target.value
            ))
    }
)
.subscribe(console.log);
```
> the event will only pass data when obs2/event2 passes data (tells us change has happend).

## switchMap() Operator
Allows us to trigger value emmitions whenever another observable emits a value.

e.g.
```js
const button = document.querySelector('button');

const obs1 = fromEvent(button, 'click');
const obs2 = interval(1000);

obs1.switchMap((event) => {
    return obs2
})
.subscribe(console.log)
```
> Whenever a button clicks an new interval gets called and subscribed but it will end the last interval observer if we click again and starts new interval observer

## BehaviorSubject
It's like Subject but has initial (default) value.

e.g.
```js
const clickEmitted = new BehaviorSubject('Not clicked!');

const button = document.querySelector('button');
const div = document.querySelector('div');

button.addEventListener(
    'click',
    () => clickEmitted.next(`Clicked!`)
);

clickEmitted.subscribe(
    (value) => div.textContent = value
);
```