# Invocation
- **函数里潜在错误在运行时才会被发现**
- The `this` parameter represents the **function context**, the object on which our function is invoked
- The `arguments` parameter represents all arguments that are passed in through a function call
## Using implicit functions parameters
- In addition to the parameters  explicitly stated in the function definition, function invocations are usually passed 2 implicit parameters: `arguments` and `this`
    - These parameters aren’t explicitly listed in the function signature, but are silently passed to the function and accessible within the function
    - They can be referenced within the function just like any other explicitly named parameter
### `arguments`
- The `arguments` object has a property named `length` that indicates the exact number of arguments
- The individual argument values can be obtained by using array indexing notation
    - Includes the excess arguments that aren’t associated with any declared function parameters
- `arguments` is not an array
    - Cannot use array methods on it
    - Think of arguments as an array-like construct, and exhibit restraint in   its use
- The `rest` parameter is a **real array**
#### `arguments` object as an alias to function parameters
- If we change the arguments object, the change is also reflected in the matching function parameter
- The same holds true in the other direction. If we change a parameter, the change can be observed in both the parameter and the `arguments` object
- Strict mode disables `arguments` aliasing (both directions)
	
    ```js
    "use strict";

    // strict mode 同时必须有分号
    (function(a) { a = 1; console.log(a, arguments[0]) })(12); 

    (function(a) { arguments[0] = 1; console.log(a, arguments[0]) })(12);
    ```

### `this`
- `this` refers to an object that’s associated with the function invocation
- It’s often termed the *function context*
- In object-oriented languages such as Java, `this` usually points to an instance of the class within which the method is defined
- In JavaScript, invoking a function as a method is only **one way** that a function can be invoked
- What the `this` parameter points to isn’t defined only by how and where the function is defined; it can also be heavily influenced by **how the function is invoked**
## Invoking functions
### Invocation as a function
- When invoked in this manner, the function context (the value of the `this` keyword) can be 2 things
    1. In nonstrict mode, it will be the global context (the `window` object)
    2. In strict mode, it will be `undefined`
- 函数内嵌套的函数也遵循同样的规则
    - https://codepen.io/slackbuffer/pen/xNQgNV?editors=1010
### Invocation as a method
- When we invoke a function as a method of an object, that object becomes the function context and is available within the function via the `this` parameter
- We don’t need to create separate copies of a function to perform the exact same processing on different objects. This is a **tenet** of object-oriented programming
### Invocation as a constructor
- There’s nothing special about a function that’s going to be used as a constructor. Constructor functions are declared just like any other functions 
- To invoke the function as a constructor, we precede the function invocation with the keyword `new`
- A function constructor enables us to create functions from dynamically created strings
    - `new Function('a', 'b', 'return a+b')`
- Constructor functions are functions that we use to **create and initialize object instances**
    - We can easily use function declarations and function expressions for constructing new objects
    - The only exception is the arrow function

```js
function showThis() {
    console.log(this)
}
const obj = { foo: showThis }

(function(bar) { 
    bar()       // Window
    obj.foo()   // {foo: f}
})(obj.foo)
```

#### Superpowers of constructors
- When invoked with the keyword `new`
    - **An empty object instance** is created
    - This object is passed to the constructor function as the `this` parameter, and thus becomes the constructor’s function context
    - The newly constructed object is returned as the `new` operator’s value
        - See exception below 
- The purpose of a constructor is to cause a new object to be created, to set it up, and to return it as the constructor value. Anything that interferes with that intent isn’t appropriate for constructors
#### Constructor return values
- If the constructor returns an object, that object is returned as the value of the whole new expression, and the newly constructed object passed as `this` to the constructor is **discarded**
- If, however, a nonobject is returned from the constructor, the returned value is **ignored**, and the newly created object is returned
#### Coding considerations for constructors
- The intent of constructors is to **initialize the new object** that will be created by the function invocation **to initial conditions**
- Such functions can be called as “normal” functions, or even assigned to object properties in order to be invoked as methods, they’re generally not useful as such
- Because constructors are generally coded and used in a manner that’s different from other functions, and aren’t all that useful unless invoked as constructors, a naming convention has arisen to distinguish constructors from run-of-the-mill functions and methods
- Functions and methods are generally named starting with a **verb** that describes what they do (skulk, creep, sneak, doSomethingWonderful, and so on) and start with a **lowercase** letter
- Constructors, on the other hand, are usually named as a **noun** that describes the object that’s being constructed and start with an **uppercase** character: Ninja, Samurai, Emperor, Ronin, and so on
- It’s easy to see how a constructor makes it more elegant to create multiple objects that conform to the same pattern without having to repeat the same code over and over
    - The common code is written just once, as the body of the constructor
### Invocation with the `apply` and `call` methods
- Event handling
	
    ```html
    <button id="test">Click Me!</button>

    <script>
        function Button(){
            this.clicked = false
            this.click = function() {
                this.clicked = true
                assert(button.clicked, "The button has been clicked") // failed
            }
        }
        var button = new Button()
        var elem = document.getElementById("test")
        elem.addEventListener("click", button.click)
    </script>
    ```

    - The event-handling system of the browser defines the context of the invocation to be the target element of the event
#### Using `apply` and `call`
- JavaScript provides a means to invoke a function and to explicitly specify any object we want as the function context
- Do this through the use of `apply` or `call` ***method*** that exists for every function
    - As the first argument, both the `call` and `apply` methods take the object that will be used as the function context
    - `apply` takes only one additional argument, an **array** of argument values; `call` takes any number of arguments, which will be used as function arguments
    - Use whichever one improves code clarity. Use the one that best matches the arguments we have handy
- As first-class objects (created by the built-in `Function` constructor), functions can have properties just like any other object type, including methods

```js
const o = { k: 'o', func: function() { console.log(this) }}
const o1 = { k: 'o1', func: () => console.log(this) }

const o2 = new Object()
o2.k = 'o2'
o2.func = () => console.log(this)

const o3 = new Object()
o3.k = 'o3'
o3.func = function() { console.log(this) }

const o4 = { k: 'o4' }

function Foo() {
    this.func = () => console.log(this)
    this.k = 'foo'
}
const o5 = new Foo()

o.func.apply(o4)    // {k: 'o4'}

o2.func()           // Window   // ATTENTION: create in the global code, not in the Object constructor, and remembers
o2.func.apply(o4)   // Window   // ATTENTION: create in the global code, not in the Object constructor, and remembers

o3.func()           // {k: 'o3', func: f}
o3.func.apply(o4)   // {k: 'o4'}

o1.func.apply(o4)   // Window

o5.func()           // {k: 'foo', func: f} remembers
o5.func.apply(o4)   // {k: 'foo', func: f} remembers
```

### Forcing the function context in callbacks
- In **imperative** programming, it’s common to **pass the array** to a method and use a `for` loop to iterate over every entry, performing the operation on each entry
	
    ```js
    function(collection) {
        for (var n = 0; n < collection.length; n++) {
            /* do something to collection[n] */
        }
    }
    ```

- The **functional approach** is to create a function that operates on a single element and **passes each entry** to that function
	
    ```js
    function(item) {
        /* do something to item */
    }
    ```

- The difference lies in thinking at a level where functions are the main building blocks of the program
- To facilitate a more **functional style**, all array objects have access to a `forEach` function that invokes a callback on each element within an array
    - It has **organizational benefits**
    - Such an iteration function could pass the current element to the callback as a parameter, but most **make the current element the function context of the callback**
- Homebrew simplified `forEach`
	
    ```js
    function forEach(list, callback) {
        for (var n = 0; n < list.length; n++) {
            callback.call(list[n], n)
        }
    }
    var var weapons = [ { type: 'shuriken' },
                        { type: 'katana' },
                        { type:'nunchucks' } ]
    forEach(weapons, function(index) {
        assert(this === weapons[index],
                "Got the expected value of " + weapons[index].type);
    })
    ```

## Fixing the problem of function contexts
- 2 more options (other thant `call` and `apply`): arrow functions and the `bind` methods
### Using arrow functions to get around functions contexts
- Arrow functions don’t have their own `this` value. Instead, they **remember** the value of the `this` parameter at the time of their **definition** (at the time they were created)
	
    ```html
    <button id="test">Click Me!</button>

    <script>
        function Button(){
            this.clicked = false
            this.click = () => {
                this.clicked = true
                assert(button.clicked, "The button has been clicked") // success
            }
        }
        var button = new Button()
        var elem = document.getElementById("test")
        elem.addEventListener("click", button.click)
    </script>
    ```

    - `apply`, `call` cannot change `this` of arrow functions; `bind` "can" because it creates a new function with a specified `this`
- Arrow functions pick up the value of the `this` parameter at the moment of their creation
#### Caveat: arrow functions and object literals
- If an arrow function is defined within an object literal that’s defined in global code, the value of the this parameter associated with the arrow function is the global `window` object

    ```html
    <button id="test">Click Me!</button>

    <script>
        var button = {
            clicked: false,
            click: () => {
                this.clicked = true
                assert(this === window, "this === window")
                assert(button.clicked, "The button has been clicked")
                assert(this === window, "In arrow function this === window")
                assert(window.clicked, "clicked is stored in window")
            }
        }
        var elem = document.getElementById("test")
        elem.addEventListener("click", button.click)
    </script>
    ```

    - The `click` arrow function is created as a property value on an object literal, and the object literal is created in global code, the `this` value of the arrow function will be the `this` value of the global code

```js
function foo() {
    const o = {}
    o.bar = () => { console.log(this) }
    o.bar()
}
const obj = { func: foo }
obj.func() // {func: f}
foo()   // Window
```

### Using `bind`
- Every function (including arrow function) has access to the `bind` method that, in short, **creates a new function**
    - This function has the **same body**, but its **context** is always **bound** to a certain object, **regardless of the way it's invoked**. In all other aspects, the bound function behaves as the original function
    - Calling the `bind` method doesn’t modify the original function
	
    ```html
    <button id="test">Click Me!</button>

    <script>
        function Button(){
            this.clicked = false
            this.click = function() {
                this.clicked = true
                assert(button.clicked, "The button has been clicked")
            }
        }
        var button = new Button()
        var elem = document.getElementById("test")
        elem.addEventListener("click", button.click.bind(button)) // a new function
    </script>
    ```

## Other `this`
### `setTimeout`
- The default `this` value of a `setTimeout` callback will still be the `window` object, and not `undefined`, even in strict mode
- There's also no option to pass a `thisArg` to `setTimeout` as there is in Array methods like `forEach`, `reduce`, etc. Using `call` to set `this` doesn't work either
    - A common way to solve the problem is to use a wrapper function that sets this to the required value
    - Another possible way to solve the "`this`" problem is to replace the host `setTimeout()` and `setInterval()` global functions with ones that allow passing a this object and set it in the callback using `Function.prototype.call`
- > https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#The_this_problem