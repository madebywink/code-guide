# Wink's Very Basic JavaScript Paradigm Guide

Code which can be reasoned about can be tested. Its behavior can be well-understood and accurately predicted by those who develop, maintain, and use it.

This guide briefly covers important concepts and patterns in JavaScript (and computer programming in general).

These concepts can be treated like axioms that inform the way we write code.

Individually they are useful, but together they are powerful. They allow code to be reasoned about in a logically and mathematically - and therefore they are one way to make code reliable and testable.

Neglecting these principles (without a set of equivalent paradigmatic principles) inevitably leads to code that is any combination of messy, unpredictable, untestable, insecure, unreliable, or vulnerable.

For a complete syntactic *style* guide, see the [AirBnB JavaScript Guide](https://github.com/airbnb/javascript).

## Table of Contents
- [Wink's Very Basic JavaScript Paradigm Guide](#winks-very-basic-javascript-paradigm-guide)
  - [Table of Contents](#table-of-contents)
  - [__E2E Testing__](#e2e-testing)
    - [__Smoke tests__](#smoke-tests)
    - [__Component / Module__](#component--module)
    - [__Unit (function / object)__](#unit-function--object)
    - [__Exploratory UX / End-user__](#exploratory-ux--end-user)
    - [__Continuous User Acceptance__](#continuous-user-acceptance)
  - [__How To Write Basically Testable Code__](#how-to-write-basically-testable-code)
    - [__The Basics__](#the-basics)
    - [__Scoping__](#scoping)
    - [__Purity__](#purity)
    - [__Closures__](#closures)
    - [__Mutability__](#mutability)
    - [__Thought Experiment: Unpredictable Users__](#thought-experiment-unpredictable-users)
    - [__Namespacing__](#namespacing)
    - [__Module Pattern__](#module-pattern)
    - [__Factory Pattern__](#factory-pattern)


## __E2E Testing__
[<sup>^ to top</sup>](#table-of-contents)

![Basic scopes and hierarchy in testing](/javascript/testinghierarchies.png) *E2E testing, as simple as possible.*

### __Smoke tests__
[<sup>^ to top</sup>](#table-of-contents)

[Smoke tests](https://en.wikipedia.org/wiki/Smoke_testing_(software)) verify the core functionality of a website, app, or product. 

Smoke tests are considered ["the most cost-effective method for identifying and fixing defects in software"](https://docs.microsoft.com/en-us/previous-versions/ms182613(v=vs.80)). 

*Smoke testing answers the question "What things do users do most often? Do those features all work?"*

These tests cover the front-end and the back-end.

### __Component / Module__
[<sup>^ to top</sup>](#table-of-contents)

[Component tests](https://sqa.stackexchange.com/questions/12630/what-is-component-testing-and-how-to-write-component-test-cases) have the power to perform deep inspection of the app's behavior.

They test the usability of each individual component or module in an app.

They are situated in the space between Smoke Tests and Unit Tests.

They can isolate Components (front end), Modules (full stack), and API's (back-end) and verify how they behave in repeatable simulated environments.

### __Unit (function / object)__
[<sup>^ to top</sup>](#table-of-contents)

The basic *unit* of behavior in terms of testing is the *function* (or the *object*, in scripting languages like JavaScript that support mutable objects).

Unit tests verify software behavior at the most atomic level: the behavior of functions and objects.

Unit tests verify things like methods, utility functions, and state stores.

### __Exploratory UX / End-user__
[<sup>^ to top</sup>](#table-of-contents)

Exploratory UX testing seeks to find bugs that are difficult to test for and to give User Acceptance insight prior to product launch.

Exploratory UX and End User testing reveals a product to a manual testing team or test population who use the product as real world users would. They provide UX and functionality feedback prior to launch, and can be charged with the task of attempting to break the app through normal use.

### __Continuous User Acceptance__
[<sup>^ to top</sup>](#table-of-contents)

Continuous User Acceptance testing crowd sources improvements via user experience feedback.

## __How To Write Basically Testable Code__
[<sup>^ to top</sup>](#table-of-contents)

### __The Basics__
[<sup>^ to top</sup>](#table-of-contents)

 - Enforce functional purity
 - Scope functions and objects correctly
 - Use Closures
 - Treat variables as immutable except where it is extremely impractical to do so, do not rely on the convenience of mutability
 - Use a toolkit of fundamental programming patterns that help enforce data safety, maintainability, and testability
 - Do not use classes (unless you must)

### __Scoping__
[<sup>^ to top</sup>](#table-of-contents)

Every function should have a single concise purpose. No more, no less.

### __Purity__
[<sup>^ to top</sup>](#table-of-contents)

Functions should either have no side effects, or a single well-defined side-effect.

In short: Every function should have a single concise purpose. No more, no less.

```js
// bad
function dirtyFunction(x, y) {
    state.value = x + y;
    return x + y
}

// bad
function setStateAndAdd(x, y) {
    state.value = x
    return x + y
}

// very bad
function setStateAndUpdateUIandAdd(x, y) {
    const sum = x+y;
    state.value = sum;
    document.querySelector('.js-element').innerText = sum
    return sum
}

// bad
function setElementText(el, text) {
    el.innerText = text;
    return el;
}

// bad
async function fetchData(apiUrl) {
    const res = await fetch(apiUrl);
    const rawData = await res.json();

    // do a bunch of data transformation operations...

    return transformedData;
}

// good
function setValue(x) {
    state.value = x;
}

// good
function add(x, y) {
    return x + y;
}

// good
function updateText(text) {
    document.querySelector('.js-text').innerText = text
}

// good!
async function fetchData(apiUrl) {
    const res = await fetch(apiUrl);
    return transformData(await res.json());
}
function transformData () { ... }
```

### __Closures__
[<sup>^ to top</sup>](#table-of-contents)

JavaScript closures are extremely powerful.

```js
// example 1
(function () {
    const hiddenVariable = "non-existent to the outside world";
})()

// example 2
someArray.map(() => {
    const hiddenVariable = "non-existent to the outside world";
    return "oh, hello!"
})
```

Objects and values that exist within closures without being revealed to the outsie world are effectively private and protected.

Closures make [Currying](https://en.wikipedia.org/wiki/Currying) possible:

```jsx
// look familiar?
function MyReactComponent() {
    const someCurriedValue = // ...;
    return (
        <div 
            onClick={() => someFunction(someCurriedValue)} 
        />
    )
}
function someFunction() { ... }
```

### __Mutability__
[<sup>^ to top</sup>](#table-of-contents)

TL;DR: Use mutability as little as possible, try to never use it unless it is simply impractical not to.

```
// mutability
let a = 1;
a = 2
```

Despite the fact that mutability is extremely convenient, when abused or used naively, it makes code very difficult to reason about.

In mathematics, the value of a variable never changes. This allows mathematicians (and computer scientists and programmers) to reliably understand how a function or statement will evaluate.

Immutability helps to create code that evaluates/executes in a predictable way. 

Nothing in JavaScript is actually immutable, but we can pretend it is.

### __Thought Experiment: Unpredictable Users__
[<sup>^ to top</sup>](#table-of-contents)

Here's something to think about:

Suppose we want to create a predictable code environment that takes user input. Maybe this environment runs on a website, an app, or an API.

How can we ensure that the code will behave in a predictable while still allowing user actions to mutate an app's internal state?

Consider how you might strictly define user actions as to prevent unwanted mutations.

Want a hint? Take a look at how the Redux library works. Consider how strictly-defined user actions allow programmers to write testable code... *and secure, maintainable code as well*.

### __Namespacing__
[<sup>^ to top</sup>](#table-of-contents)

Namespacing is made possible through the combined use of scopes and closures. It's a fundamental way to keep data safe and to write clean, maintainable, testable code.

### __Module Pattern__
[<sup>^ to top</sup>](#table-of-contents)

The module pattern in JavaScript is very power, very important, and very natural to use.

```js
export default (function myModule() {
    'use strict';
    const _privateVariable = 42;
    const _state = {
        currentValue: 'hello'
    }
    function _setState() {
        // ...
    }
    function someUtility(x, y) {
        // ...
        return // ...;
    }
    return {
        // bad
        dangerouslyExposeState: () => _state,
        // good
        setState: (x) => _setState(x),
        state: () => ({..._state}),
        // good
        someUtility
    }
})()

```

### __Factory Pattern__
[<sup>^ to top</sup>](#table-of-contents)

The factory pattern in JavaScript is also very powerful, very important, and relatively straight-forward to use.

There is more than one way to program a factory. It comes down to correctly using JS and Programming fundamentals.

Note that this makes use of scopes, closures, currying, and namespacing.

```js
const defaultConfig = {
    // ...
}

export default function FactoryBuilder(
    config = {...defaultConfig, ...options}
) {
    const _state = {currentValue: config.initalValue}
    function _setState() { /* ... */ }
    const methods = {
        // bad
        dangerouslyExposeState: () => _state,
        // good
        setState: (x) => _setState(x),
        state: () => ({..._state}),
    }

    return () => Object.create(methods)
}

const myConfig = { /* ... */ }
const configuredFactory = FactoryBuilder(myConfig);
const statefulObject = configuredFactory();

// now let's run some tests...
```

\* *The combined use of the module pattern and prototypes allows us all of the potential benefits of `class`-based objects, including inheritance.*