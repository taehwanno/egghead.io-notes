# [Learn how to use Immutable.js](https://egghead.io/courses/learn-how-to-use-immutable-js)

- [x] 1. Manage Application State with Immutable.js
- [x] 2. Creating an Immutable Object Graph with Immutable.js Map()
- [x] 3. Modifying an Immutable.js Map()
- [x] 4. Querying an Immutable.js Map()
- [x] 5. Iterating Over an Immutable.js Map()
- [x] 6. Working with Subsets of an Immutable.js Map()
- [x] 7. Equality Checking with .is() and More
- [x] 8. Using fromJS() to Convert Plain JavaScript Objects into Immutable Data
- [x] 9. Differences between the Immutable.js Map() and List()
- [x] 10. Exploring Sequences and Range() in Immutable.js
- [x] 11. Converting Immutable.js Structures to Javascript and other Immutable Types
- [x] 12. Simple Undo/Redo with Immutable.js Data Structures
- [x] 13. Maintaining Order in Immutable.js Objects
- [x] 14. Transforming Immutable Data with Reduce
- [x] 15. Lightning Fast Immutable.js Equality Checks with Hash Codes
- [x] 16. Immutable.Record() as data models

## [1. Manage Application State with Immutable.js](https://egghead.io/lessons/javascript-manage-application-state-with-immutable-js)

> Learn how Immutable.js data structures are different from native iterable Javascript data types and why they provide an excellent foundation on which to build your application's state.

## [2. Creating an Immutable Object Graph with Immutable.js Map()](https://egghead.io/lessons/javascript-creating-an-immutable-object-graph-with-immutable-js-map)

> Learn how to create an Immutable.Map() through plain Javascript object construction and also via array tuples.

## [3. Modifying an Immutable.js Map()](https://egghead.io/lessons/javascript-modifying-an-immutable-js-map)

> We will now look at five methods that modify an Immutable.Map(). I highly encourage you to visit the Immutable.js documentation where I am now. They are set, delete, clear, update and merge. These are used often, so let's get to know them well.

## [4. Querying an Immutable.js Map()](https://egghead.io/lessons/javascript-querying-an-immutable-js-map)

> Learn how to query an Immutable.Map() using get, getIn, has, includes, find, first and last. These are powerful operators that make finding data in an object graph pain free.

## [5. Iterating Over an Immutable.js Map()](https://egghead.io/lessons/javascript-iterating-over-an-immutable-js-map)

> Immutable.js provides several methods to iterate over an Immutable.Map(). These also apply to the other immutable structures found within the Immutable.js family, such as Set and List. The primary methods are map and forEach, but we will also cover filter and groupBy.

- `map`
- `filter`
- `forEach` (*Side Effect*)
- [`groupBy`](https://facebook.github.io/immutable-js/docs/#/Map/groupBy)

```js
const { List, Map } = require('immutable@4.0.0-rc.9')
const listOfMaps = List([
  Map({ v: 0 }),
  Map({ v: 1 }),
  Map({ v: 1 }),
  Map({ v: 0 }),
  Map({ v: 2 })
])
const groupsOfMaps = listOfMaps.groupBy(x => x.get('v'))
// Map {
//   0: List [ Map{ "v": 0 }, Map { "v": 0 } ],
//   1: List [ Map{ "v": 1 }, Map { "v": 1 } ],
//   2: List [ Map{ "v": 2 } ],
// }
```

## [6. Working with Subsets of an Immutable.js Map()](https://egghead.io/lessons/javascript-working-with-subsets-of-an-immutable-js-map)

> Immutable.js offers methods to break immutable structures into subsets much like Array--for instance it has the all powerful slice()--and unlike Array it offers functional methods like take() and skip(). You get the best of both the imperative and functional worlds.

- `slice`
- `takeLast`
- `butLast`
- `rest`
- `skip`
- `skipUntil`
- `skipWhile`

## [7. Equality Checking with .is() and More](https://egghead.io/lessons/javascript-equality-checking-with-is-and-more)

> Equality checking deeply nested objects is painful. Fortunately Immutable.js makes this task easy with its .is() and .isSubset() methods. Let's see how we can take two different Immutable Maps() and check for equality.

- `is`
- `isSubset`
- `isSuperset`

## [8. Using fromJS() to Convert Plain JavaScript Objects into Immutable Data](https://egghead.io/lessons/javascript-using-fromjs-to-convert-plain-javascript-objects-into-immutable-data)

> Immutable.js offers the fromJS() method to build immutable structures from objects and array. Objects are converted into maps. Arrays are converted into lists. The fromJS() method can also take a reviver function for custom conversions.

- `fromJS` (`Object` -> `Immutable.Map`, `Array` -> `Immutable.List`)

## [9. Differences between the Immutable.js Map() and List()](https://egghead.io/lessons/javascript-differences-between-the-immutable-js-map-and-list)

> The Immutable.js Map() is analogous to a Javascript Object or Hash since it is comprised of key-value pairs. The Immutable.js List() is analogous to a Javascript Array and contains many of the same native methods. Let's compare the two and dive into the basics of List().

- `Immutable.List.of`

## [10. Exploring Sequences and Range() in Immutable.js](https://egghead.io/lessons/javascript-exploring-sequences-and-range-in-immutable-js)

> Understanding Immutable.js's Map() and List() structures will likely take you as far as you want to go with immutable programming. They have only small semantic differences between each other and the remaining structures in the Immutable.js family. Sequence, however, has one major difference: it's lazy--which opens a new realm of functional possibilities. Let's write a simple sequence to start.

- `Immutable.Seq`

```js
import _ from 'lodash';

describe('Exploring Sequences and Range() in Immutable.js', () => {
  it('should see that Seq() is lazy', () => {
    const range = _.range(1000);
    let numberOfOperations = 0;

    let powerOfTwo = Immutable.Seq.of(...range)
      .map((num) => {
        numberOfOperations+=;
        return num * 2;
      });

      expect(numberOfOperations).to.equal(0);
      powerOfTwo.take(10).toArray();
      expect(numberOfOperations).to.equal(10);
  });

  it('should not produce an overflow with infinite Range()', () => {
    let powerOfTwoRange = Immutable.Range(1, Infinity);

    expect(powerOfTwnRange.size).to.equal(Infinity);
    first1000Powers = powerOfTwoRange
      .take(1000)
      .map(n => n * 2);

    expect(first1000Powers.size).to.equal(1000);
  });
});
```

## [11. Converting Immutable.js Structures to Javascript and other Immutable Types](https://egghead.io/lessons/javascript-converting-immutable-js-structures-to-javascript-and-other-immutable-types)

> Immutable.js provides several conversion methods to migrate one structure to another. Each Immutable.js class contains a prefixed "to" method like Map.toList(), Map.toSet(), etc. Converting these types sometimes results in a loss of data, as we will see when converting from Map to List.

- `toMap`
- `toOrderedMap`
- `toSet`
- `toOrderedSet`
- `toList`
- `toStack`

```js
mocha.setup('bdd');
const expect = chai.expect;

describe('Converting Immutable.js Structures to Javascript and other Immutable Types', () => {
  it('should convert Map() to List()', () => {
    const map = Immutable.Map({
      key1: 'First Item',
      key2: 'Second Item'
    });

    const convertedList = map.toList();

    expect(Immutable.List.isList(convertedList)).to.be.true;

    // Keys are discarded
    expect(convertedList.first()).to.equal('First Item');
    expect(convertedList.last()).to.equal('Second Item');
  });

  it('should convert List() to Map()', () => {
    const list = Immutable.List.of('First Item', 'Second Item');
    const convertedMap = list.toMap();

    // Converted keys ascend numerically
    keys = convertedMap.keys();
    expect(keys.next().value).to.equal(0);
    expect(keys.next().value).to.equal(1);

    expect(Immutable.Map.isMap(convertedMap)).to.be.true;

    expect(convertedMap.first()).to.equal('First Item');
    expect(convertedMap.last()).to.equal('Second Item');
  });

  it('should convert Map() to Javascript Array', () => {
    const map = Immutable.Map({
      key1: 'First Item',
      key2: 'Second Item',
      key3: {key4: 'Nested Item'}
    });

    const arr = map.toArray();

    // Keys are discarded
    expect(arr[0]).to.equal('First Item');
    expect(arr[1]).to.equal('Second Item');
    expect(arr[2].key4).to.equal('Nested Item');

  });

  it('should convert Map() to JSON', () => {

    const map = Immutable.Map({
      key1: 'First Item',
      key2: 'Second Item',
      key3: {key4: 'Nested Item'}
    });

    const json = map.toJSON();

    expect(json.key1).to.equal('First Item');
    expect(json.key2).to.equal('Second Item');
    expect(json.key3.key4).to.equal('Nested Item');

  });

});

mocha.run();
```

## [12. Simple Undo/Redo with Immutable.js Data Structures](https://egghead.io/lessons/javascript-simple-undo-redo-with-immutable-js-data-structures)

> Implementing Undo/Redo can be tricky business. Consider two strategies to simplify implenting undo/redo: 1) use immutable data 2) react to that data with a top-down approach. Let's build a simple drawing app that will draw circles on a canvas and allow us to undo/redo our drawing.

## [13. Maintaining Order in Immutable.js Objects](https://egghead.io/lessons/javascript-maintaining-order-in-immutable-js-objects)

> Immutable.js's Map doesn't promise iteration order after a set() operation. When we need a guarantee for iteration order, we can use OrderedMap. This comes at a slight performance cost, or for you computer science people, it is amortized at an unstable O(log32 N). Let's explore ordering.

## [14. Transforming Immutable Data with Reduce](https://egghead.io/lessons/javascript-transforming-immutable-data-with-reduce)

> Immutable.js iterables offer the reduce() method, a powerful and often misunderstood functional operator on which map(), filter(), groupBy(), etc. are built. The concept is simple: reduce transforms your iterable into something else, that's all. The name is misleading as you may or may not actually "reduce" anything. Let's replicate the groupBy() and filter() methods with reduce to illustrate how it works.

## [15. Lightning Fast Immutable.js Equality Checks with Hash Codes](https://egghead.io/lessons/javascript-lightning-fast-immutable-js-equality-checks-with-hash-codes)

> While Immutable.js offers .is() to confirm value equality between iterables it comes at the cost of referencing each key and value in both objects. For lightning fast equality checks, Immutable.js can produce a hash code based on an iterable's content. If two iterables have the same content, their hash codes will be the same. It's worth noting that this technique is unsuitable for mission critical application development since there is a chance, however slight, that checksums like these might collide. This is outlined here: https://en.wikipedia.org/wiki/Collision_(computer_science)

## [16. Immutable.Record() as data models](https://egghead.io/lessons/javascript-immutable-record-as-data-models)

> The Immutable.js Record() allows you to model your immutable data much like you would model data with native Javascript classes or objects. It differs from native classes because it cannot be mutated after it's creation and it always has a default value. It's an excellent construct in which to piece together your stores, be them Flux or some other storage implementation. Let's quickly create an Immutable Record().
