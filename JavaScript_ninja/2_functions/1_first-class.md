# First-class objects
- Understand JS as a functional language
- Functions are first-class **objects** (first-class citizens) created by the built-in `Function` constructor
- Functions are objects, just with an additional, special capability of being ***invokable***: Functions can be called or invoked in order to perform an act
- Function capabilities
    - Referenced by variables
    	
        ```js
        function whatsMyContext() { return this }
        // This doesn’t create a second instance of the function;
        // it merely creates a reference to the same function (first-class object)
        var getMyThis = whatsMyContext
        ```
    
    - Created via literals
    - Assigned to variables, array entries, properties of objects
    - Passed as arguments to functions
    - Returned as values from functions
    - Can posses properties that can be dynamically created and assigned

    ```js
    var foo = function() {}
    foo.name = 'ho'
    ```

## Callback functions
- One of the most important features of JavaScript is the ability to **create functions in the code anywhere an expression can appear**
- A callback can be called asynchronously or **synchronously**
### Sorting with a comparator
- `arr.sort(cb(a, b))`
    - Provide a function that performs comparison; give the sort algorithm access to this function as a callback; the algorithm calls the callback whenever it needs to make a comparison
    - The callback is expected to return a **positive** number if the order of the passed values should be **reversed**, a negative number if not, and zero if the values are equal
        - `return a - b`: `a > b`; reversed; `[b, a]`; ascending
        - `return b - a`: `a > b`; `[a, b]`; descending
## Functions as objects
### Storing functions
- Storing a collection of **unique** functions
	
    ```js
    var store = {
        nextId: 1,
        cache: {},
        add: function(fn) {
            if (!fn.id) {
                fn.id = this.nextId++;
                this.cache[fn.id] = fn;
                return true;
            }
        }
    }
    function ninja() {}
    store.add(ninja)
    ```

    - If the function has an `id` property, we assume that the function has already been processed and we ignore it
### Self-memorizing functions
- Memoization is the process of building a function that’s capable of remembering its previously computed values
    - Whenever a function computes its result, we store that result alongside the function arguments
    - When another invocation occurs for the same set of arguments, we can return the previously stored result, instead of calculating it anew
	
    ```js
    function isPrime(value) {
        if (!isPrime.answers) {
            isPrime.answers = {}
        }
        if (isPrime.answers[value] !== undefined) {
            return isPrime.answers[value]
        }

        var prime = value !== 1
        for (var i = 2; i < value; i++ ) {
            if (value % i === 0) {
                prime = false
                break
            }
        }
        return isPrime.answers[value] = prime
    }
    ```

    - Cache is a property of the function itself, so it’s kept alive for as long as the function itself is alive
- Disadvantages
    - Any sort of caching will certainly sacrifice memory in favor of performance
    - Purists may consider that caching is a concern that shouldn’t be mixed with the business logic; a function or a method should do one thing and do it well
    - It’s difficult to load-test or measure the performance of an algorithm such as this one, because results depend on the previous inputs to the function
## Define functions
- 4 ways
    1. Function declarations and function expressions
    2. Arrow functions (lambda functions)
    3. [Function constructors](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function)
    4. Generator functions
        - We can define generator versions of function declarations, function expressions, and function constructors
### Function declarations and function expressions
#### Function declarations
- A function declaration must be placed on its own, as a separate JavaScript statement (but can be contained within another function or a block of code)
#### Function expressions
- Such functions that are always a part of another statement (for example, as the right side of an assignment expression, or as an argument to another function) are called function expressions
	
    ```js
    (function() { console.log(123) })()
    // Named function expression as part of a function call that will be immediately invoked
    (function namedFunctionExpression() {})()

    // Function expressions that will be immediately invoked, as **arguments to unary operators**
    +function() {}();
    -function() {}();
    !function() {}();
    ~function() {}();
    ```

- For function declarations, the function name is **mandatory**, whereas for function expressions it’s completely **optional**
#### Immediate functions
- When we want to make a function call, we use an expression that evaluates to a function, followed by a pair of function call parentheses
    - The expression to the left of the calling parenthesis doesn’t have to be a simple identifier; it can be ***any expression that evaluates to a function***
    - A simple way to specify an expression that evaluates to a function is to use a function expression
    - > Immediately invoked function expression (IIFE)
- IIFE can be used to mimic modules in JS
- Parentheses around function expressions
    - The reason is purely **syntactical** - the JavaScript parser has to be able to easily differentiate between function declarations and function expressions
    - If we leave out the parentheses around the function expression, and put our immediate call as a separate statement `function(){}(3)`, the JavaScript parser will start processing it, and will conclude, because it’s a separate statement starting with the keyword `function`, that it’s dealing with a function declaration 
    - Because every function declaration has to have a name (and here we didn’t specify one), an error will be thrown
    - To avoid this, we place the function expression within parentheses, signaling to the JavaScript parser that it’s dealing with an expression, and not a statement
    - There’s also an alternative, even simpler way (yet, strangely, a little less often used) of achieving the same goal: `(function(){}(3))`. By wrapping the immediate function definition and call within parentheses, you can also notify the JavaScript parser that it’s dealing with an expression
- Unary operators signal to the JavaScript engine that it’s dealing with expressions and not statements
    - The results of applying these unary operators aren’t stored anywhere; from a computational perspective, they don’t really matter; only the calls to our IIFEs matter
### Arrow functions
- `=>` - *fat-arrow* operator
## Arguments and function parameters
- A *parameter* is a variable that we list as part of a function **definition**
- An *argument* is a value that we pass to the function when we **invoke** it
### Rest **parameters**
- 是数组
- Only the last function parameter can be a rest parameter
- 可以用于箭头函数；**可以只有 rest parameters** 没有别的参数
	
    ```js
                // 括号不能省略 
    const bar = (...params) => console.log(params)
    ```

### Default **parameters**
- In other programming languages, this problem is most often solved with function overloading (specifying additional functions with the same name but a different set of parameters)
- 后面的 default parameters 可以引用前面的 parameters
    - 不要这样写