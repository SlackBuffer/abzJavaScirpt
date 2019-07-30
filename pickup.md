- `<a href="javascript:void(0)"></a>`
    - `void expression`
    - The `void` operator evaluates the given expression and then returns `undefined`
        - This operator allows evaluating expressions that produce a value into places where an expression that evaluates to `undefined` is desired
        - The `void` operator is often used merely to obtain the `undefined` primitive value, usually using `void(0)` (which is equivalent to `void 0`)
    - When a browser follows a `javascript: URI`, it evaluates the code in the URI and then replaces the contents of the page with the returned value, unless the returned value is `undefined`
    - > https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void