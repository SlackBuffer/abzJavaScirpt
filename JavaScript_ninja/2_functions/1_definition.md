# First-class objects
- Understand JS as a functional language
- Functions are first-class **objects** (first-class citizens)
- Functions are objects, just with an additional, special capability of being ***invokable***: Functions can be called or invoked in order to perform an act
- Function capabilities
    - Referenced by variables
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