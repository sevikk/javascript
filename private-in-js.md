#### Factory functions and closures

```js
function Hedgehog() {
  let speed = 10000; // this is private
  this.name = 'Sonic';
  this.zoom = function() {
    // both name and speed are accessible from here
    console.log(`${this.name} zooms with the speed of ${speed} miles per second!`);
  }
}

const sonic = new Hedgehog();
sonic.zoom();

console.log(sonic.name) //valid value
console.log(sonic.speed) // undefined
```

The code above is a typical example of attaining encapsulation via factory function and closure. 
While the implementation is pretty straightforward, it does come with a memory usage penalty. 
The reason is because method zoom will be recreated for every new instance of the Hedgehog function

-----------------------------

#### Weak maps and namespaces 

The memory penalty caused by using factory functions and closures can be avoided by using WeakMap objects to store 
the private members. The penalty is avoided since one WeakMap can be used to store private members of multiple 
instances of the class.

```js
let Hedgehog = (function() {
  let privateProps = new WeakMap();
  
  class Hedgehog {
    constructor(name) {
      this.name = name; // this is public
      privateProps.set(this, {
        speed: 1000
      }); // this is private
    }

    zoom() {
      console.log(`${this.name} zooms with the speed of ${privateProps.get(this).speed} miles per second!`);
    }
  }

  return Hedgehog;
})();

let sonic = new Hedgehog('Sonic');
console.log(sonic.zoom());
```

There’s a reason why we chose WeakMap for storage instead of using a regular Map or a plain JavaScript object: 
Weak maps allow you to use JavaScript objects as keys. Other kinds of dictionary collections will only let you 
use primitives. Memory leaks are avoided because WeakMap holds weak references to its items. This allows those 
items to be garbage collected when they loose all other references. Weak maps guarantee that objects are only 
accessible using get method. WeakMap does not have any other methods for accessing its items(looping, Object.keys() and etc).

-----------------------------

#### Using Symbols (kind of encapsulated)

```js
let Hedgehog = (function() {
  const speed = Symbol();
  class Hedgehog {
    constructor(name) {
      this.name = name;
      this[speed] = 1000; // this is not directly accessible
    }

    zoom() {
      console.log(`${this.name} zooms with the speed of ${this[speed]} miles per second!`);
    }
  }

  return Hedgehog;
})();


let sonic = new Hedgehog('Sonic');
sonic.zoom();
```

---------------------

Easy way 

The easiest way to achieve pseudo-encapsulation would be to prefix your private member with a special symbol 
that indicates the private scope to the client. It is a common convention to use the _ symbol as a prefix.

