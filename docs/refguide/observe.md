# Observe

Given an observable `object` and a `listener` method, `observe(object, listener)` starts listening for changes on the given object.
The callbacks are invoked with values as described in the specs of 
[Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe)
and [Array.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/observe).

Unlike `autorun`, `observe` does not respect transactions and is always invoked immediately after altering an object. This makes it useful for invariant checking or input transformations.
But note that in most cases `autorun` is a better and more powerful alternative to observe. Especially for scalars and objects.

Accepted objects are:
* Observable maps (created using `mobx.map(object)`). Possibly emitted event types are: `"add"`, `"update"` and `"delete"`.
* Observable objects (created using `mobx.observable(object)`). Possibly emitted event types are `"update"`, and in combination with `extendObservable`, `"add"` might fire as well.
* Observable arrays (created using `mobx.observable(array)`). Possibly emitted event types are `"update"` and `"splice"`.
* Plain objects will be passed through `mobx.observable` first. See 'Observable objects' for further details. 
* Plain arrays are _not_ accepted by `observe`. Pass them through `mobx.observable` first.
* Observable object / map + property name (string) can be used to observe a single property.

The function returns a `disposer` function that can be used to cancel the observer.
Note that `transaction` does not affect the working of the `observe` method(s). This means that even inside an transaction `observe` will fire its listeners for each mutation.
Hence `autorun` is usually a more powerful alternative.

Example:

```javascript
import {observable, observe} from 'mobx';

const person = observable({
	firstName: "Maarten",
	lastName: "Luther"
});

const disposer = observe(person, (change) => {
	console.log(change.type, change.name, "from", change.oldValue, "to", change.object[change.name]);
});

person.firstName =  "Martin";
// Prints: 'update firstName from Maarten to Martin'

disposer();
// Ignore any future updates

// observe a single field
const disposer2 = observe(person, "lastName", (newValue, oldValue) => {
	console.log("LastName changed to ", newValue);
});
```


Related blog: [Object.observe is dead. Long live mobx.observe](https://medium.com/@mweststrate/object-observe-is-dead-long-live-mobservable-observe-ad96930140c5)