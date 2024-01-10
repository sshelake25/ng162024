https://rxmarbles.com/
https://angular.io/guide/rx-library

RxJS (Reactive Extensions for JavaScript) is a library for reactive programming using Observables. It provides a set of powerful features for handling asynchronous and event-based code. Here are some important RxJS features with examples:

### 1. Observables:

Observables represent a stream of values or events over time. They can be subscribed to, and you can react to the values emitted by the observable.

```typescript
import { Observable } from 'rxjs';

// Creating an observable
const observable = new Observable<number>((observer) => {
  observer.next(1);
  observer.next(2);
  observer.next(3);
  setTimeout(() => observer.next(4), 1000);
});

// Subscribing to the observable
observable.subscribe((value) => console.log(value));
```

### 2. Operators:

Operators are functions that allow you to transform, filter, and manipulate the data emitted by observables.

```typescript
import { of } from 'rxjs';
import { map, filter } from 'rxjs/operators';

// Using operators to transform and filter data
of(1, 2, 3, 4, 5)
  .pipe(
    map((value) => value * 2),
    filter((value) => value > 5)
  )
  .subscribe((value) => console.log(value));
```

### 3. Subjects:

Subjects are both observable and observer. They can multicast values to multiple observers.

```typescript
import { Subject } from 'rxjs';

// Creating a subject
const subject = new Subject<number>();

// Subscribing to the subject
subject.subscribe((value) => console.log('Observer 1:', value));

// Broadcasting values from the subject
subject.next(1);

// Another observer subscribing to the same subject
subject.subscribe((value) => console.log('Observer 2:', value));

// Broadcasting another value
subject.next(2);
```

### 4. Error Handling:

You can handle errors in the observable chain using error handling operators.

```typescript
import { of } from 'rxjs';
import { map, catchError } from 'rxjs/operators';

// Handling errors in the observable chain
of(1, 2, 'a', 4, 5)
  .pipe(
    map((value) => {
      if (typeof value !== 'number') {
        throw new Error('Invalid value encountered');
      }
      return value * 2;
    }),
    catchError((error) => {
      console.error('Error:', error.message);
      return of(0); // Recover by emitting a default value
    })
  )
  .subscribe((value) => console.log(value));
```

### 5. Debouncing and Throttling:

Debouncing and throttling are techniques to control the rate of events emitted by observables.

```typescript
import { fromEvent } from 'rxjs';
import { debounceTime, throttleTime } from 'rxjs/operators';

// Debouncing
const debouncedObservable = fromEvent(document, 'keyup').pipe(debounceTime(300));
debouncedObservable.subscribe(() => console.log('Debounced event'));

// Throttling
const throttledObservable = fromEvent(document, 'mousemove').pipe(throttleTime(1000));
throttledObservable.subscribe(() => console.log('Throttled event'));
```

These are just a few examples of the many features provided by RxJS. RxJS is a powerful library that enables you to write expressive and concise code for handling asynchronous operations and events in a reactive manner. Explore the [RxJS documentation](https://rxjs.dev/) for a comprehensive list of features and detailed explanations.
