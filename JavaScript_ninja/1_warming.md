# JavaScript
- JavaScript is quite functionally oriented
- Important concepts
    - Functions are first-class objects
        - Functions coexist with, and can be treated like, any other JavaScript object
        - They can be created through literals, referenced by variables, passed around as function arguments, and even returned as function return values
    - Function closures
        - A function is a closure when it actively maintains (“closes over”) the external variables used in its body
    - Scopes
    - Prototype-based object orientation
- JavaScript consists of a close relationship between objects and prototypes, and functions and closures
- Useful features
    - Generators
    - Promises
    - Proxies
    - Advanced array methods
    - Maps, sets
    - Regular expressions
    - Modules
- Transpilers (“transformation + compiling”)
    - Take cutting-edge JavaScript code and transform it into equivalent (or, if that’s not possible, similar) code that works properly in most current browsers
# Browsers
- DOM (Document Object Model)
- Events
- Browser API
# Using current best practices
- Debugging
    - Browser dev tools
- Testing

    ```js
    // assert is not a standard feature of JS
    assert(condition, message)
    assert(a === 1, "Disaster! a is not 1!")
    ```

- Performance analysis

    ```js
    // built-in
    console.time("My operation");               // start a named timer
    for(var n = 0; n < maxCount; n++){          // performs the operation multiple times
    /* perform the operation to be measured */
    }
    console.timeEnd("My operation");            // stop the timer
    ```

# Transferability
- The browser wars ended with HTML, CSS, the DOM API, and JavaScript all being standardized
- Desktop applications
    - These technologies usually wrap the browser so that we can build desktop UIs with standard HTML, CSS, and JavaScript, with additional support that makes it possible to interact with the filesystem
- Mobile apps with frameworks
    - Frameworks for mobile apps use a wrapped browser but with additional platform-specific APIs that let us interact with the mobile platform
- Server-side applications and applications for embedded devices with Node.js
# **Lifecycle** of a client-side web application
- 2 steps
    1. **Page building** - set up the user interface
    2. **Event handling** - enter a loop waiting for events to occur, and start invoking event handlers
    - > The lifecycle follows similar phases as other GUI application (think standard desktop applications or mobile applications)
# Page-building
- 2 steps
    1. Parsing the HTML and building the DOM
    2. Executing JavaScript code
- Step 1 is performed when the browser is processing HTML nodes, and step 2 is performed whenever a special type of HTML element—the `script` element is encountered
- During the page-building phase, the browser can switch between these 2 steps as many times as necessary. These 2 steps are repeated as long as there are HTML elements to process and JavaScript code to execute
- When the browser runs out of HTML elements to process, the page-building phase is complete. The browser then moves on to the second part of the web application lifecycle: event handling
## Parsing the HTML and building the DOM
- The page-building phase starts with the browser receiving the HTML code
- The browser does the parsing one HTML element at a time until the first `script` element is reached
- Although the HTML and the DOM are closely linked, with the DOM being constructed from HTML, they aren’t one and the same
- You should think of the HTML code as a **blueprint** the browser follows when constructing the initial DOM - the UI - of the page
    - The browser can even fix problems that it finds with this blueprint in order to create a valid DOM
    - > HTML: https://html.spec.whatwg.org/, https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5
    - > DOM: https://dom.spec.whatwg.org/, https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model
- When the browser encounters the `script` element during page construction, the browser pauses the DOM construction from HTML code and starts executing JavaScript code
## Executing JavaScript code
- The browser provides an API through a global object that can be used by the JavaScript engine to interact with and modify the page
- https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction
### Global objects
- The primary global object that the browser exposes to the JavaScript engine is the `window` object, which represents the window in which a page is contained
- **The `window` object** is the global object through which all other global objects, global variables (even user-defined ones), and browser APIs are accessible
- One of the most important properties of the global window object is the `document`, which represents the DOM of the current page
### Different types of JavaScript code
- We broadly differentiate between 2 different types of JavaScript code: *global code* and *function code*
    - The code contained in a function is called function code
    - The code placed outside all functions is called global code
- Global code is executed automatically by the JavaScript engine in a straightforward fashion, line by line, as it’s encountered
- Function code, in order to be executed, has to be called by something else: either by global code, by some other function, or by the browser
### Executing JavaScript code in the page-building phase
- When the browser reaches the `script` node in the page-building phase, it **pauses** the DOM construction based on HTML code and starts executing JavaScript code instead
    - This means executing the global JavaScript code contained in the script element, and functions called by the global code are also executed
- JavaScript cannot select and modify elements that haven’t yet been created
    - That’s one of the reasons people tend to put their script elements at the **bottom** of the page. That way, we don’t have to worry about whether a particular HTML element has been reached
- Once the JavaScript engine executes the last line of JavaScript code in the `script` element, the browser exits the JavaScript execution mode and continues building DOM nodes by processing the remaining HTML code
- If, during that processing, the browser again encounters a `script` element, the DOM creation from HTML code is again paused, and the JavaScript runtime starts executing the contained JavaScript code
- **The global state of the JavaScript application persists in the meantime**
    - All user-defined global variables created during the execution of JavaScript code in one script element are normally accessible to JavaScript code in other script elements
- This happens because the global `window` object, which stores all global JavaScript variables, is alive and accessible during the entire lifecycle of the page
# Event handling
- Client-side web applications are GUI applications, which means they react to different kinds of events: mouse moves, clicks, keyboard presses, and so on
- For this reason, the JavaScript code executed during the page-building phase, in addition to **influencing the global application state** and **modifying the DOM**, can also **register** event listeners (or handlers): functions that are **executed by the browser** when an event occurs
- The browser execution environment is, at its core, based on the idea that **only a single piece of code can be executed at once**: the so-called *single-threaded* execution model
- 事件不会等前一个事件结束才发生
- The browser uses an *event queue* to keep track of the events that have occurred but have yet to be processed
- All generated events (it doesn’t matter if they’re user-generated, like mouse moves or key presses, or server-generated, such as Ajax events) are placed in the same event queue, in the order in which they’re detected by the browser
- Event-handling process
    - The browser checks the head of the event queue
    - If there are no events, the event loop keeps checking
    - If there’s an event at the head of the event queue, the browser takes it and executes the associated handler (if one exists). During this execution, the rest of the events patiently wait in the event queue for their turn to be processed
- This event loop will continue executing until the user closes the web application
- Writing event handlers that take a lot of time to execute leads to unresponsive web applications
- The browser mechanism that puts events onto the queue is ***external*** to the page-building and event-handling phases
    - The processing that’s necessary to **determine when events have occurred** and that **pushes them onto the event queue** doesn’t participate in the thread that’s handling the events
- The handling of events, and therefore the invocation of their handling functions, is **asynchronous**
- The following types of events can occur, among others
    - Browser events, such as when a page is finished loading or when it’s to be unloaded
    - Network events, such as responses coming from the server (Ajax events, serverside events)
    - User events, such as mouse clicks, mouse moves, and key presses
    - **Timer** events, such as when a timeout expires or an interval fires
- Code is set up in advance in order to be executed at a later time
    - Except for global code, the vast majority of the code we place on a page will execute as the result of some event
## Registering event handlers
- Before events can be handled, our code has to notify the browser that we’re interested in handling particular events
- In client-side web applications, there are 2 ways to register events
    - By assigning functions to special properties
        - It’s only possible to register one function handler for a particular event. It’s easy to **overwrite** previous event-handler functions
    - By using the built-in `addEventListener` method
        - OK to register many function handlers for a particular event