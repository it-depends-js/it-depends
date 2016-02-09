[![Build status](https://ci.appveyor.com/api/projects/status/dufdv2sefowej3mq?svg=true)](https://ci.appveyor.com/project/gerich-home/it-depends)

# it-depends
Lightweight dependency tracking / caching library for JavaScript

## About the library
**it-depends** library is the attempt to create a lightweight dependency tracking library that helps you organize your JavaScript code using the approach to build a model proposed in [KnockoutJS](http://knockoutjs.com/documentation/computedObservables.html)

**it-depends** does not worry about UI - it is solely for managing dependencies between data/model elements.

The key point in using the library is to define as less **state values** as possible and define the rest of the model as **computed values**, based on them.

All computed values are **lazy** and **cacheable**. It means that if you define the computed value based on other values it is not calculated immediately.

The calculation function **will be called** on the **first** attempt to read the value.

The calculation function **will not be called** immediately after the value of any dependency is changed.
You **must read** the value again to trigger the execution of calculation function.

## When to use?
Your code has quite a little state and a lot of values that can be computed from this state.

Your aim is to have easy to test, debug and maintain self-describing pieces of code **without side effects**.
You are OK with building code that way (it looks similar to [Functional Programming](https://en.wikipedia.org/wiki/Functional_programming)).

You have some state that should be calculated on demand only and you do not want to clutter your code with additional caching logic.

You use a framework that is responsible for rendering the model on the screen.

Limitation(**to be removed**): the framework is built in Angular way - it makes decisions when it is better to query data from the model by itself and **does not require** you to notify that some value has changed (see #3, #5 - when they will be implemented, the limitation will be removed)

## History
I created the library when I was refactoring one of complex screens in an [AngularJS](https://angularjs.org/) application.

The corresponding UI code contained logic to handle the checked/unchecked state of checkboxes mixed with the quite complex business rules for calculating whether the checkbox is enabled or disabled (based on the checked/unchecked state).

The code was slow because for any (even unrelated to checkboxes) change on UI Angular restarted `$digest` loop and requeried the checked and enabled states.
I preferred to do not use `$scope.$watch` to update values on change in checked/unchecked state, because I did not want to clutter my scope with implementation details of intermediate values, I wanted to always have some exact state (without intermediate transition states, when some values were updated and some of them - not) and I did not want to manage dependencies manually.

I refactored code to extract these calculation functions into small pieces, implcitly declared dependencies between them, so the framework became able to handle both dependencies and caching for me.

After that I allowed AngularJS to query my `$scope` so that it could get actual values from observale values and cached computed. 

## Installation and usage

#### [NodeJS](https://nodejs.org/)

```
npm install git://github.com/gerich-home/it-depends.git
```

In your application include the module and use it:
```
var itDepends = require('it-depends').itDepends;

// your code goes here:
var firstName = itDepends.value('James');
...
```

#### [AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)/[RequireJS](http://requirejs.org/)

Download the [latest release](https://github.com/gerich-home/it-depends/releases)

Place where it is suitable according to your AMD/RequireJS configuration

Use it as a dependency of AMD module:
```
define(['it-depends'], function(itDepends) {
    // your code goes here:
    var firstName = itDepends.value('James');
    ...
});
```

#### Browser globals

Download the [latest release](https://github.com/gerich-home/it-depends/releases)

Place where it is suitable

Include it into the page:
```
<script src="js/ext/it-depends.js"></script>
<script src="js/your-application.js"></script>
```

or
```
<script src="js/ext/it-depends.min.js"></script>
<script src="js/your-application.js"></script>
```

You will be able to use `itDepends` global variable in your-application.js:
```
// your code goes here:
var firstName = itDepends.value('James');
...
```

##### Example code
```
var firstName = itDepends.value('James');
var lastName  = itDepends.value('Bond');

var fullName = itDepends.computed(function(){
    return 'Hello, ' + firstName() + ' ' + lastName();
});

console.log(fullName()); // Hello, James Bond

firstName('Jack');

console.log(fullName()); // Hello, Jack Bond
```

## Contributing
I will be glad if you will join the development.

There are lot of things you can help me with. Here are few of them:
* Enhance documentation / write samples
* Raise/discuss/prepare PRs for [improvements/issues](https://github.com/gerich-home/it-depends/issues)
* Add more [unit tests](https://github.com/gerich-home/it-depends/tree/master/specs)
* Help me with publishing to different repositories, automate it: npm, Bower, NuGet
* Move to TypeScript / write typings
* Make library known to publicity

## Author
[Sergey Gerasimov](mailto:gerich.home@gmail.com)

## License
MSPL (Microsoft Public License)
