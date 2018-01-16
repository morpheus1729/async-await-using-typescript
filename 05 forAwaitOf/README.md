# Asynchronous iteration using for-await-of
> The `for-await-of` syntax is similar to the `for-of` iteration. The key difference is that it automatically awaits any promises generated by the iterator. This lesson covers how to consume async data sources easily with `for-await-of`.

> `for-await-of` essentially allows you to use `async/await` in a generator function.

***Have the demo window open***

* Here we have a simple generator function that returns numbers one by one.
  * We increment the number and if it becomes more than 10 we stop.
* We can use generator functions in standard synchronous javascript with a simple `main` function
  * and we use `forof` to loop through all the items returned by the numbers function.
  * Then we log out the number
* Lets invoke the main function and observe its behavior

```js
function* numbers() {
  let index = 1;
  while (true) {
    yield index;
    index = index + 1;
    if (index > 10) {
      break;
    }
  }
}

function main() {
  for (const num of numbers()) {
    console.log(num);
  }
}
main();

```

***Run the code***
You can see that it behaves as expected logging out `10` numbers.


***Delete the main function to prevent infinity loop***
Now lets imagine that we are going to get the next number from an external source like a backend service that will perform its magic to increment the index.

```js
import { magic } from './server'; // NEW LINE

function* numbers() {
  let index = 1;
  while (true) {
    yield index;
    index = magic(index);         // REPLACE LINE, Show error
    if (index > 10) {
      break;
    }
  }
}
```

***however info on magic(index)***
* Here we have a type mismatch.
* We have a promise to a number and we would really like to have the resolved `number` to make our decision for the loop termination.


This can be done easily with, (you guessed it) async await.

```js
import { magic } from './server';

async function* numbers() {      // ADD ASYNC
  let index = 1;
  while (true) {
    yield index;
    index = await magic(index);  // ADD AWAIT
    if (index > 10) {
      break;
    }
  }
}

```
***select async function***
Before the `for await of` JavaScript feature, a generator function could not be async. This new feature requires a runtime polyfill to work correctly called the `asyncIterator` symbol.

We can provide it easily here, alternatively you can use a library like core-js that will polyfill it for you.

```js
(Symbol as any).asyncIterator =
  (Symbol as any).asyncIterator
  || Symbol.for("Symbol.asyncIterator");
```

* This `numbers` function is an async generator and returns an async iterator. Similar to how we use `for of` loops with synchronous iterators, we can use `for await of` loops with async iterators.

```js
async function main() {
  for await (const num of numbers()) {
    console.log(num);
  }
}
main();
```
***Run the code***
You can see the for await of loop works as expected, getting a number asynchronously and logging it out.

***Select the `async` in `numbers`, `await` in `number`, for await in `main`***
To summarize, when you want to use `async await` in generators, you can use `for await of` to iterate its results.