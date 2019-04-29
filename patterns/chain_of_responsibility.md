# Chain of Responsibility

An object (the sender) makes a request which is passed along a chain of other objects (the receivers), which handle (or ignore) the request before they pass it along. The sender only recognises the first receiver, and each receiver only recognises its next sibling. The request continues along the chain until it is handled or it reaches the end of the chain without it being handled at all.

Implementation in the real world: [Event-handling propagation in the DOM (bubbling, capturing)](https://stackoverflow.com/a/4616720).

## Example code

```javascript
/**
 * The receiver object, one of many in the chain.
 * @class
 */
class Receiver {
  /**
   * @constructor
   * @param {string} data - a representation of data in the receiver object.
   */
  constructor(data) {
    this.data = data;
    this.next = null;
  }

  /**
   * Set the next receiver to follow on from this receiver object.
   * @param {Receiver} receiver - the next sibling.
   */
  setNextReceiver(receiver) {
    this.next = receiver;
  }

  /**
   * Handling method. In this case it compares an argument to the data that is stored in this receiver object, logging to console if a match has been found.
   * @param {string} str - string to compare with data in current Receiver.
   */
  handle(str) {
    if (str === this.data) {
      console.log(`Argument '${str}' has a match in the chain, argument handled.`);
      return;
    }

    // No match found in this object, so check for a next sibling in the chain and pass the string to that sibling to handle
    if (this.next) {
      this.next.handle(str);
      return;
    }

    // No match found, and this object is at the end of the chain
    console.log(`Argument '${str}' has no match -- it is left unhandled as the chain has reached its end.`);
  }
}

/**
 * The chain object.
 * @class
 */
class Chain {
  /**
   * @constructor
   */
  constructor() {
    // Add new receiver objects to handle data one-by-one in the chain.
    this.hello = new Receiver('hello');
    this.world = new Receiver('world');
    this.foo = new Receiver('foo');
    this.bar = new Receiver('bar');

    // Link each receiver object
    this.hello.setNextReceiver(this.world);
    this.world.setNextReceiver(this.foo);
    this.foo.setNextReceiver(this.bar);
  }

  /**
   * Start the chain at its head.
   * @param {string} str - string to pass through the chain until a match is found.
   */
  trigger(str) {
    this.hello.handle(str);
  }
}

/**
 * Basic illustration (a simple function) of a 'sender' role within this design pattern.
 */
const sender = () => {
  // Data to pass into the chain
  const data = ['bar', 'baz'];

  const chain = new Chain();
  chain.trigger(data[0]); // first request is handled in the chain
  chain.trigger(data[1]); // second request is not handled in the chain
}
```
