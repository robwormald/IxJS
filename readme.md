[![Build Status](https://travis-ci.org/ReactiveX/IxJS.svg?branch=master)](https://travis-ci.org/ReactiveX/IxJS)

# The Interactive Extensions for JavaScript (IxJS)... #

*...is a set of libraries to compose synchronous and asynchronous collections and [Array#extras](http://blogs.msdn.com/b/ie/archive/2010/12/13/ecmascript-5-part-2-array-extras.aspx) style composition in JavaScript*

The Interactive Extensions for JavaScript (IxJS) is a library for creating and composing asynchronous sequences using both the `Iterable` and `AsyncIterable` classes.

## `Iterable`

The `Iterable` class a way to create and compose synchronous collections much like Arrays, Maps and Sets in JavaScript using the Array#extras style using the familiar methods you are used to like `map`, `filter`, `reduce` and more.

```js
// ES
import * as Ix from 'ix';

// CommonJS
const Ix = require('ix');

Ix.Iterable.from([1,2,3,4])
  .filter(x => x % 2 === 0)
  .map(x => x * 2)
  .forEach(x => console.log(`Next ${x}`));

// => 4
// => 8
```

Alternatively, we can use the [`for ... of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) statements to iterate our collections.

```js
// ES
import * as Ix from 'ix';

// CommonJS
const Ix = require('ix');

const results = Ix.Iterable.from([1,2,3,4])
  .filter(x => x % 2 === 0)
  .map(x => x * 2);

for (let item of results) {
  console.log(`Next ${x}`);
}

// => 4
// => 8
```

Instead of bringing in the entire library for `Iterable`, we can pick and choose which operators we want, for bundling concerns.

```js
// ES
import { IterableX as Iterable } from 'ix/iterable';
import 'ix/add/iterable/of';
import 'ix/add/iterable-operators/map';

// CommonJS
const Iterable = require('ix/iterable').IterableX;
require('ix/add/iterable/of');
require('ix/add/iterable-operators/map');

const results = Iterable.of(1,2,3)
  .map(x => x + '!!');
```

We can also bring in only the operators that we want to using just the operators themselves.  Many of these operators take a simple `Iterable` source such as an `Array`, `Map`, `Set` or generator function such as our `map` and `filter` functions.

```js
// ES
import { map } from 'ix/iterable/map';
import { filter } from 'ix/iterable/filter';

// CommonJS
const map = require('ix/iterable/map').map;
const filters = require('ix/iterable/filter').filter;

const source = [1,2,3];
const results = map(
  filter(
    source, 
    x => x % 2 === 0
  ),
  x => x * x
);

for (let item of results) {
  console.log(`Next: ${item}`);
}
```

We can mix the two approaches by adding the minimal `chain` operator which then allows us to create a more fluent style but yet keeping the surface area to a minimum.

```js
// ES
import { IterableX as Iterable } from 'ix/iterable';
import 'ix/add/iterable-operators/chain';
import { of } from 'ix/iterable/of';
import { filter } from 'ix/iterable/filter';
import { map } from 'ix/iterable/map';

// CommonJS
const Iterable = require('ix/iterable').IterableX;
require('ix/add/iterable-operators/chain');
const of = require('ix/iterable/of');
const filter = require('ix/iterable/filter');
const map = require('ix/iterable/map');

const results = of(1, 2, 3)
  .chain(source => filter(source, x => x % 2 === 0))
  .chain(source => map(source, x => x * x));

for (let item of results) {
  console.log(`Next: ${item}`);
}
```

The `Iterable` object implements the iterator pattern in JavaScript by exposing the `[Symbol.iterator]` method which in turn exposes the `Iterator` class.  The iterator yields values by calling the `next()` method which returns the `IteratorResult` class.

```typescript
interface Iterable<T> {
  [Symbol.iterator](): Iterator<T>;
}

interface Iterator<T> {
  next(value?: any): IteratorResult<T>;
  return?(value?: any): IteratorResult<T>;
  throw?(e?: any): IteratorResult<T>;
}

interface IteratorResult<T> {
  value: T;
  done: Boolean;
}
```

## `AsyncIterable`

The `AsyncIterable` object is based off the ECMAScript Proposal for [Asynchronous Iterators](https://github.com/tc39/proposal-async-iteration).  This would allow us to create asynchronous collections of Promises and be able to use such methods as the `map`, `filter`, `reduce` and other Array#extras methods that you are used to using.

```js
import * as Ix from 'ix';

// CommonJS
const Ix = require('ix');

async function* gen() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
}

Ix.AsyncIterable.from(gen())
  .filter(x => x % 2 === 0)
  .map(x => x * 2)
  .forEach(x => console.log(`Next ${x}`))
  .catch(err => console.log(`Error ${err}`));

// => 4
// => 8
```

Much like with the `Iterable` object where we can iterate through our collections, we can use `for await ... of` instead which allows us to iterate over the asynchronous collection.

```js
import * as Ix from 'ix';

// CommonJS
const Ix = require('ix');

async function* gen() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
}

const results = Ix.AsyncIterable.from(gen())
  .filter(x => x % 2 === 0)
  .map(x => x * 2);

for await (let item of results) {
  console.log(`Next ${x}`);
}

// => 4
// => 8
```

Instead of bringing in the entire library for `AsyncIterable`, we can pick and choose which operators we want, for bundling concerns.

```js
// ES
import { AsyncIterableX as AsyncIterable } from 'ix/asynciterable';
import 'ix/add/asynciterable/of';
import 'ix/add/asynciterable-operators/map';

// CommonJS
const AsyncIterable = require('ix/asynciterable').AsyncIterableX;
require('ix/add/asynciterable/of');
require('ix/add/asynciterable-operators/map');

const results = AsyncIterable.of(1,2,3)
  .map(x => x + '!!');
```

We can also bring in only the operators that we want to using just the operators themselves.  Many of these operators take a simple `AsyncIterable` source from `async function*` functions such as the `map` and `filter` functions.

```js
// ES
import { map } from 'ix/asynciterable/map';
import { filter } from 'ix/asynciterable/filter';

// CommonJS
const map = require('ix/asynciterable/map').map;
const filter = require('ix/asynciterable/filter').filter;

const source = async function* () {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
};

const results = map(
  filter(
    source(), 
    x => x % 2 === 0
  ),
  x => x * x
);

for await (let item of results) {
  console.log(`Next: ${item}`);
}
```

Much like with the `Iterable` object, we can mix the two approaches for the `AsyncIterable` object by adding the minimal `chain` operator which then allows us to create a more fluent style but yet keeping the surface area to a minimum.

```js
// ES
import { AsyncIterableX as AsyncIterable } from 'ix/asynciterable';
import 'ix/add/asynciterable-operators/chain';
import { from } from 'ix/asynciterable/from';
import { filter } from 'ix/asynciterable/filter';
import { map } from 'ix/asynciterable/map';

// CommonJS
const Iterable = require('ix/asynciterable').IterableX;
require('ix/add/asynciterable-operators/chain');
const from = require('ix/asynciterable/from');
const filter = require('ix/asynciterable/filter');
const map = require('ix/asynciterable/map');


const source = async function* () {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
};

const results = from(source())
  .chain(source => filter(source, async x => x % 2 === 0))
  .chain(source => map(source, async x => x * x));

for await (let item of results) {
  console.log(`Next: ${item}`);
}
```

The `AsyncIterable` class implements the async iterator pattern in JavaScript by exposing the `[Symbol.asyncIterator]` method which in turn exposes the `AsyncIterator` class.  The iterator yields values by calling the `next()` method which returns a Promise which resolves a `IteratorResult` class.

```typescript
interface AsyncIterable<T> {
  [Symbol.asyncIterator](): AsyncIterator<T>;
}

interface AsyncIterator<T> {
  [Symbol.asyncIterator](): AsyncIterator<T>;
  next(value?: any): Promise<IteratorResult<T>>;
  return?(value?: any): Promise<IteratorResult<T>>;
  throw?(e?: any): Promise<IteratorResult<T>>;
}

interface IteratorResult<T> {
  value: T;
  done: Boolean;
}
```

## Converting from Iterable to AsyncIterable

Using IxJS, you can easily go from an `Iterable` to an `AsyncIterable` using a number of methods.  First, we can use the `from` function, either as a standalone or on the `Ix.AsyncIterable` object.  The `from` method accepts a standard `Iterable`, `Generator`, and `Iterator` of Promises, or even another `AsyncIterable`.

```js
import { from } from 'ix/asynciterable/from';
import { map } from 'ix/asynciterable/map';

const xs = [1, 2, 3, 4];
const asyncIterable = from(xs);

const mapped = map(asyncIterable, async (item, index) => item * index);

for await (let item of mapped) {
  console.log(`Next: ${item}`);
}
```

In addition, you can use the specialized async methods that are suffixed with `Async`, such as `mapAsync`, `filterAsync`, `flatMapAsync` amongst others. These functions accept async functions which allow you to return a `Promise` as the result. 

```js
import { mapAsync } from 'ix/iterable/mapasync';

const xs = [1, 2, 3, 4];
const mapped = mapAsync(xs, async (item, index) => item * index);

for await (let item of mapped) {
  console.log(`Next: ${item}`);
}
```

## License ##

The MIT License (MIT)

Copyright (c) ReactiveX

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
