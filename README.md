
# Variable scope

This assignment will cover variable scope in JavaScript. In doing so, we will explore one of the more complex scope-related concepts: closures. By the end, you should have an understanding of how to create local variables in JavaScript, how and why to avoid global variables, and how to work with and create closures.

## Prerequisites

* functions
* variables
* loops

## How to follow along

You'll get the most out of this assignment by trying out for yourself all of the examples provided. Make an empty file, eg. `test.js`, and simply running `node test.js` whenever you want to try out some code. This allows you to tweak code and try it again! Add `console.log()` statements if you want to see output in your terminal.

Unless directed otherwise, record any exercise answers in a file called `scope_answers.md`.


## Overview

*Scope* describes what is available at any given time and place in your program. For example, a variable is only *in scope* after it is declared. Here is a simple example:

```js
number = number + 5;
```

The above example throws an error on the first line; `number is not defined`. This is because the variable `number` is not *in scope* - it's not declared anywhere. Let's add a line:

```js
var number = 0;
number += 5;
```

Now we've declared a variable called `number` on the first line and initialized it to `0`. `number` is now in scope, and so we successfully add 5 to it.

If all programs were this simple, programming would be easy! Let's explore some specific cases.


>NOTE:
Remember to always use `var` when declaring a new variable! Otherwise, the variable will become *global* (instead of *local*). We'll come back to this later.


## Scope is Functional

Scope JavaScript is considered to be *functional*. That is, any variable you declare with the `var` keyword (ie. a *local* variable) is available anywhere inside that function, and is NOT available outside of the function.

Look at the following function:

```js
function walk() {
  var target = 'home';
  console.log('Target:', target);
}
```

It declares a variable and prints out a message. We should therefore see that message if we call `walk()`:

```js
walk(); // Target: home
```

What if we try to simply print out the value of `target`?

```js
console.log('Target:', target); // ReferenceError: target is not defined
```

Because the variable was declared inside the function, it is not available outside of the function. This hopefully is hopefully what we expect! Ruby works the same way when declaring local variables inside methods.

In the following example, we therefore shouldn't expect either attempt to print out `my_value` to work:

```js
console.log(my_value); // ReferenceError: my_value is not defined

function setValue() {
  var my_value = 7;
}
setValue();

console.log(my_value); // The program won't make it here, but it would also error
```

What if we declare a variable outside of a function? Can we access it inside? Let's try that out:

```js
var target = 'the store';
function run() {
  console.log('Target:', target);
}
run(); // Target: the store
target = 10;
run();
```

The answer is yes! _Unlike Ruby_, a local variable declared outside of a function is accessible inside the function.

JavaScript uses functions all over the place. It is likely that you've been using them more than you realize! It is important to know, however, because scope depends entirely on it. Below is an example of an issue you might run into with functional scope. Note that this example will NOT run in node - it is written for the browser.

```js
// wait for the document to be ready
document.addEventListener("DOMContentLoaded", function() {

  function assembleMouseTrap() {
    // ...
  }


});

assembleMouseTrap();
```

This example will error with:

```
ReferenceError: assembleMouseTrap is not defined
```

Why? Because you are actually defining a function inside of another function. Look closely at the `document.addEventListener` line and you'll notice that it ends with `function() {`. `assembleMouseTrap` is therefore defined inside of that function, and so it is not available outside of that function.

As it turns out, this happens all the time in JavaScript. As we'll see later, we sometimes declare functions within functions intentionally!

You can think about JavaScript scope in terms of 'levels'. Anything defined on the top level (not in any function) is accessible anywhere (it is globally scoped). Any function or local variable defined in a function is available anywhere inside that function - including functions nested within that function - and is NOT available outside of that function.

>Note: the below code is a contrived example for demonstration purposes - it actually results in an infinite loop if you call any of the functions!

```js
// Top level; anything defined here is available anywhere

function doStuff() { // defined at top level, so available anywhere
  performActions();

  function doStuffSubFunction() { // defined inside doStuff -> not available outside of doStuff()
    doStuff();
    performActions();
    doStuffSubFunction2();
  }

  function doStuffSubFunction2() { // defined inside doStuff -> not available outside of doStuff()
    doStuff();
    performActions();
    doStuffSubFunction();
  }
}

function performActions() { // defined at top level -> available anywhere
  doStuff();

  function performActionsSubFunction() { // defined inside performActions -> not available outside of performActions()
    performActions();
    doStuff();
  }
}
```

We can see that any defined variable is available anywhere 'below' its current location. Or, we can call/use anything defined at or 'above' our current position in the code.

`doStuffSubFunction()` has access to `doStuff()` and `performActions`, because both are defined 'above'.
`doStuffSubFunction()` DOES NOT have access to `performActionsSubFunction()`, because it is not defined 'above' or 'beside'.


## Exercise 1

What is the value output in the given `console.log()` statements? Read them and guess *before* running the code. For each answer, explain (keep it short) why the output came out that way.

Make sure to READ the code. The goal of this exercise is to train your ability to successfully read and predict the outcome of JavaScript code.

Feel free to talk with other students to try to understand the result.

#### Example:
```js
var dalmatians = 101;
console.log(dalmatians);
```

1. Read the code.
2. Run the code.
3. Explain why it worked the way it did.

Answer:
The second line will output 101 because the variable dalmatians was defined on the line above.

That's it!

#### Exercises:
```js
var lastWord = 'welcome';
console.log(lastWord);
lastWord = 'goodbye';
```

```js
var message = "Up here!";

function shout() {
  console.log(message);
}

shout();
```

```js
var message = "Up here!";

function shout(message) {
  console.log(message);
}

shout("Down below!")
```

```js
var muffins = 'two dozen';
var purchasedMuffins;

function getMuffins() {
  return muffins;
}
purchasedMuffins = getMuffins();
console.log(purchasedMuffins);
```

```js
var chore = 'laundry';

function doChores() {
  var chore = 'sneak out';

  function reportActivity() {
    console.log(chore);
  }

  reportActivity();
}

doChores(); // calling doChores(), which then calls reportActivity()
```

```js
var letter;
var contents = 'Looking for gold';

function getMail() {

  function changeContents() {
    var contents = 'Struck it rich!';
  }

  changeContents();
  return contents;
}

letter = getMail();
console.log(letter);
```

```js
var decision;

function firstIdea() {
  var decision = 'Buy a new car';
  return decision;
}

function secondIdea() {
  console.log(decision);
}

firstIdea();
secondIdea();
```



## Blocks

We've said already that JavaScript has *functional* scope. How does scope work with blocks? For example, within a `for` loop:

```js
for (var i = 0; i < 5; i++) {
  var greeting = 'Hi Friend!';
}
console.log(greeting); // Hi Friend!
```

Even though we defined a variable inside of a for loop, it was accessible to us outside (after) the loop. This is because blocks have no special scope in JavaScript. It's functions or nothing! Compare this to using the `map` function to double the prices in our store:

```js
var storePrices = [1, 4, 5, 9];
var newStorePrices = storePrices.map(function(oldPrice) {
  var newPrice = oldPrice * 2;
  return newPrice;
});
console.log(newPrice); // ReferenceError: newPrice is not defined
```

This may look surprising because `newPrice` is clearly defined above our `console.log(newPrice)` line. However, `newPrice` is defined inside of a function, and so it shouldn't be accessible anywhere other than inside of that function. The function just happens to be *anonymous* and defined *inline*, meaning that a) it has no name and b) we're not taking the time to separately define a function. Let's do it the other way too:

```js
var storePrices = [1, 4, 5, 9];

function doublePrice(oldPrice) {
  var newPrice = oldPrice * 2;
  return newPrice;
}

var newStorePrices = storePrices.map(doublePrice);
console.log(newPrice); // ReferenceError: newPrice is not defined
```

Now it's much clearer that `newPrice` is defined somewhere inaccessible - 'below' our current level in the code. It is therefore only accessible inside of that function.

To recap - blocks in JavaScript are NOT special (unlike blocks in Ruby). They do not get their own scope. Scope is based only on functions.


## Exercise 2

For each problem below, READ the code and try to figure out what will happen. Then run the code to see if you're right. Lastly, perform the follow-up rewrite provided after each problem.


##### Problem 1
Can we access the `address` variable outside of the `buildHouse` method?
```js
function buildHouse(address) {
  // ... house gets built
  return 'Building house at ' + address;
}
buildHouse('123 Happy St.');
console.log(address);
```
Modify the above example so it prints out the address (just the address) instead of erroring. You shouldn't have to modify the function.

##### Problem 2

What's the danger here?  We're declaring a variable inside of an `if` statement.
```js
var determined = false;
if (determined) {
  var smoothie = 'strawberry banana';
}
console.log(smoothie);
```
Modify the above example so that `smoothie` exists whether `determined` is `true` or `false`.

##### Problem 3

What happens to the variable `index` after the for loop finishes?
```js
for (var index = 0; index < 5; index++) {
  // ...
}

console.log(index);
```
Modify the above example so that the variable `index` is NOT available when we use `console.log`. Remember, scope in JavaScript is *functional*.

##### Problem 4

What's the difference between `for` and `forEach`? One answer is that they differ in scope.
```js
var items = ['glasses', 'toothpaste', 'wallet'];
items.forEach(function(item) {
  var lastItem = item;
});
console.log(lastItem);
```
Modify the above example so that `lastItem` is available when we run `console.log(lastItem)`.




## Local vs Global Variables

All of the examples so far have used local variables. This is because you should avoid global variables. Global variables are attractive to beginners because they seem easier to work with at first.

Global variables are accessible in ANY file, anywhere in your application. This makes it very hard to 'trust' your variables because they can be changed anywhere! Tracking down bugs under those conditions becomes nearly impossible.

Let's say we have the following code:

```js
leaveWork();
console.log(available); // ReferenceError: available is not defined
```

This works as expected so far. Now imagine that the `leaveWork()` function *is in a different file* and looks as follows:

```js
function leaveWork() {
  available = true; // not using 'var'
}
```

Our original code will no longer error! The function `leaveWork()` is setting a global variable - note the lack of `var` when the variable is declared - and is interfering with our code elsewhere. For this reason, we always want to limit the scope of our variables as much as possible.

> ##### Summary
* Rethink your solution if you are tempted to use global variables.
* Always use `var` to set new variables (or `let` and `const` if you're using ES6 -- read more about them here: https://hacks.mozilla.org/2015/07/es6-in-depth-let-and-const/)






-----




# Closures

This part of the assignment will provide an explanation of closures and a walkthrough of how to use them.

Some important notes:

* You *will* use closures whether you understand them or not. This is basically unavoidable in modern JavaScript wed applications.
* You may have used closures already without realizing it!

## What is a closure?

A *closure* is a function that remembers a piece of information, and remembers it no matter what. The application can do anything it wants, but the function (or closure) still hangs on to that old piece of information. Let's first explore a couple of things that are NOT closures.

```js
function helloWorld() {
  console.log("Hello world!");
}
```

This isn't a closure; it's just a function. It's not remembering any data. We've typed `"Hello world!"` in there, but it is hardcoded, and so we won't count it as 'remembering'.

```js
function sayHello(name) {
  console.log("Hello, " + name + "!");
}
```

This is also not a closure. It does allow us to pass in a name, but it won't later remember which name we passed it. This is easy to test by calling it twice in a row.

```js
sayHello('Alfred'); // outputs "Hello, Alfred!"
sayHello(); // outputs "Hello, undefined!"
```

We can see that it did not 'remember' the name Alfred, or anything at all. The function is simply doing what it is told each time.

Let's try another example that is NOT a closure. It will, however, be closer to what we need to do to make one.

```js
var number = 'old number';
function callMeOnMyCellPhone() {
  console.log(number);
}

callMeOnMyCellPhone(); // old number
```

This appears to be remembering the phone number we gave it! But a closure has to hang on to that data, *no matter what*. Let's try to change the number and see what happens:

```js
number = 'new number';
callMeOnMyCellPhone(); // new number
```

The function is now printing out the new number. So this is neat, and it might be useful, but it is still not a closure.

What is an example of a closure, then? We'll get to that, but first, let's see an example of where you might want to use one.


## Event Handlers

Event handlers are probably the most common place to find closures. They are responsible for firing when something happens, and they often need to know information about (say) the list element that was clicked.

Let's try to accomplish the following: we want a number of list elements to output some information when clicked. They should print the content of the list element as well as their order (starting at 1, ie. 1, 2, 3, etc.).

Read the code found in `closureAttempt1.js`. Try to figure out what the code will do when you click on the `h1` or `p` tags. When you are done, make sure `index.html` is pointing to `closureAttempt1.js` and open `index.html` in your browser - it will NOT run in node. Click on the heading and paragraph elements and observe!

...

...

...

...

...

...

...

...

...

...

...

...

...

...

Wait, what? Why did that happen? Let's figure it out.

## The Big Mistake (that everybody makes)

It would seem reasonable to expect the page to provide an alert with the appropriate secret message. But they both provided the same message!

Nearly every JavaScript developer has at some point been tripped up by this exact issue. To understand it, let's go back to our `callMeOnMyCellPhone` example.

```js
var number = 'old number';
function callMeOnMyCellPhone() {
  console.log(number);
}

callMeOnMyCellPhone(); // old number
number = 'new number';
callMeOnMyCellPhone(); // new number
```

The function was relying on `number`, and so when we pointed `number` to a new value, the function followed suit.

> ##### JavaScript Variable Scope
Remember how scope in JavaScript works? A variable defined *outside* of a function is available *inside* that function. We haven't re-declared `number` inside the function, so `number` inside the function is identical to `number` outside the function.

The example in the browser was actually very similar (`secretMessage` instead of `number`), despite looking more complicated:

```js
function alertWithMessage() {
  alert(secretMessage); // note that secretMessage is not set here
}

var h1 = document.querySelector('h1');
var paragraph = document.querySelector('p');

var secretMessage = "I'll be in the clock tower at midnight.";
h1.addEventListener('click', alertWithMessage);

secretMessage = "The object of interest is in the museum.";
paragraph.addEventListener('click', alertWithMessage);
```

The important parts are:

* Create a function that will be our click handler. It relies on `secretMessage` being set.
* Set `secretMessage` to the first message.
* Set up a click handler on `h1` to use the click handler.
* Change `secretMessage` to the second message.
* Set up a second click handler on `paragraph` to use the click handler.

Put like that, we can see that both click handlers are relying on the same click handler, which relies on the same variable: `secretMessage`. This is exactly what happened with `callMeOnMyCellPhone`: it was relying on `number`. We should therefore expect clicking either element to alert the same message. We'll come back to this later to see a solution.

Try reading this section more than once before moving on. If you are unfamiliar with JavaScript scope and how it works, you'll want to play around with it yourself! Make a JS file, create variables and functions, run them in Node, and learn as much as you can. Tackling closures without an understanding of scope is like climbing a mountain without first climbing a hill.

## Hiding Information

We've seen that the problem with the previously proposed solution was that the value of `secretMessage` kept changing. What if we could find a way to guarantee that the variable won't change ever again? Putting it another way, if we could make the variable 'safe' from the rest of the program, then each item on the page should have its own secret message.

Closures work by protecting their variables from change. It sounds fancy, but they are simply taking advantage of concepts you already know about! Let's work toward the knowledge required to write our own closure and fix the problem.

The first thing to remember is that scope in JavaScript is *functional*. That is, a variable (or a parameter!) created inside of a function does not exist outside of the function. We can use this to protect variables from outside influence.

Let's work toward a closure with our `callMeOnMyCellPhone` example.

```js
function outerFunction() {
  var number = 'old number';
  function callMeOnMyCellPhone() {
    console.log(number);
  }
}
```

What's changed?
* we put the `callMeOnMyCellPhone` function into another function called `outerFunction`
* `outerFunction` defines a variable called `number`

In short, we put the entire `callMeOnMyCellPhone` example into another function!

This isn't quite what we want just yet, but let's talk about what we've accomplished. The variable `number` is not available outside of `outerFunction` where it is defined. It should also be available to the function `callMeOnMyCellPhone` because they are both inside the same function.

We have therefore solved the issue of being able to manipulate `number` wherever we want and seeing the function act different. Unfortunately, this code doesn't really *do* anything yet. All we're doing is defining a function (inside of another function). Let's double check what happens in Node:

```
> outerFunction();
undefined
```

`outerFunction` is not printing or returning anything, so we should expect to just get `undefined`. Our goal is to get at `callMeOnMyCellPhone`. How can we do that? Let's try to return it!

```js
function outerFunction() {
  var number = 'old number';
  function callMeOnMyCellPhone() {
    console.log(number);
  }
  return callMeOnMyCellPhone;
}
```

Running this in Node:
```
> outerFunction()
[Function: callMeOnMyCellPhone]
```

We're getting a function back. Now we need to find a way to call that function. We can call it right off the bat, or save it into a variable to call later. Trying out both:

```js
var callMe = outerFunction();
callMe(); // prints 'old number'
callMe(); // prints 'old number' again
outerFunction()(); // prints 'old number'
```

It looks like it remembers the value `'old number'`. Let's now double check that we can't change that value ourselves.

```js
var callMe = outerFunction();
callMe(); // prints 'old number'
number = 'new number';
callMe(); // prints 'old number'
```

Like we predicted before, we have no control over the variable `number` that `outerFunction` defined and `callMeOnMyCellPhone` relies on.

We have now officially created a closure! It's not a very useful one because it is incapable of printing anything except `'old number'`. Ideally we can somehow pass `outerFunction` a different number. But we already know how to pass a function information.

```js
function outerFunction(number) {
  function callMeOnMyCellPhone() {
    console.log(number);
  }
  return callMeOnMyCellPhone;
}
```

The only change is to delete the line where we defined `number`, and instead add a parameter to `outerFunction`. Let's first try this out without passing it anything.

```js
var callMe = outerFunction();
callMe(); // undefined
```

We got `undefined`, which makes sense because we didn't pass anything. Let's try to pass a new number.

```js
var callMe = outerFunction('555 5555');
var callMeYesterday = outerFunction('old number');
var callMeTomorrow = outerFunction("tomorrow's number");
callMe(); // 555 5555
callMeYesterday(); // old number
callMeTomorrow(); // tomorrow's number
callMe(); // 555 5555
callMeYesterday(); // old number
```

`outerFunction` therefore builds for us a little function that prints out a specific thing. It's like a function factory. And the reason we did it was because our previous effort was very fragile - every time we changed the value of the variable `number`, the function changed with it.

Finally, we don't really need to name the inner function `callMeOnMyCellPhone`, or anything at all. Let's return an anonymous function instead.

```js
function outerFunction(number) {
  return function() {
    console.log(number);
  }
}
```

## Back to the Problem

Reviewing `closureAttempt1.js` again to try to pinpoint the issue:

```js
function alertWithMessage() {
  alert(secretMessage); // note that secretMessage is not set here
}

var h1 = document.querySelector('h1');
var paragraph = document.querySelector('p');

var secretMessage = "I'll be in the clock tower at midnight.";
h1.addEventListener('click', alertWithMessage);

secretMessage = "The object of interest is in the museum.";
paragraph.addEventListener('click', alertWithMessage);
```

The issue is that `secretMessage` keeps changing! Each item on the page (the `h1` and `p` tags) gets an event handler (a function) that refers to whatever value `secretMessage` had *at the time it was clicked*, which is the always second message, rather than when we added the event handlers.

We therefore want `secretMessage` to be private and unchangeable. What can we do about this? The first thing to note is that the event handler is a function. That's half of a closure! If we want to make `secretMessage` private, we have to somehow wrap that portion of the code in a function. We'll then have a function-in-a-function.

Take a look at `closureAttempt2.js`. All we've done is moved the setting of event handlers into their own functions. This solves the problem because we are passing the outer function a value for `secretMessage`. The inner function (the event handler itself) is able to remember this value (the parameter `message`) and, most importantly, the change in value of `secretMessage` is unable to affect that value of the parameter `message` because `message` is defined inside of a function (as a parameter).

Update `index.html` to point to `closureAttempt2.js` and check for yourself that it works.


In the name of repetition, our problem was that we had a variable (`secretMessage`) that was accessible to the outside world, meaning that we couldn't rely on it keeping the same value. The two things we used to solve our problem were:

* A function that uses a variable without defining it.
  * This is true of the _inner_ functions in all of our examples.
* The variable needs to be 'private' and safe from outside tampering.
  * That's why we have an outer function with a parameter. It protects the inner function and acts like a shell.

A closure provides precisely these two things. A function wrapped in a function allows for passing information to the outer function, while the inner function simply remembers that information.

Next, take a look at `closureAttempt3.js`. This is the same solution! We have simply cleaned it up by using an anonymous function and not setting variables if we don't need to (eg. `firstMessageClickHandler`).


## Exercise 1

The file `closure_exercises.js` provides a number of exercises (ignore `advanced.js` for now). Your task is to fill in the missing code. In each example, you are trying to get the code to run successfully. Use the code at the top and bottom of the exercises to determine what you need to accomplish.

Note as well that each closure in the exercises simply generates output (ie. `console.log()`). In the real world, closures are made to solve problems, and so they are almost always more complex.



## Exercise 2

Fill in the event handlers found in `closure_exercises/advanced.js`. Each problem will have its own goal described in comments.








# Additional Resources

https://medium.freecodecamp.com/javascript-closures-explained-by-mailing-a-package-4f23e9885039

http://stackoverflow.com/a/7464475/659816

http://stackoverflow.com/questions/111102/how-do-javascript-closures-work
