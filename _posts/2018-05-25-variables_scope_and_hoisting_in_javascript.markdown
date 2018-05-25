---
layout: post
title:      "Variables, Scope, and Hoisting in JavaScript"
date:       2018-05-25 10:13:50 -0400
permalink:  variables_scope_and_hoisting_in_javascript
---


The question at the heart of this post is, 'How does JavaScript find a variable when it needs it?'  It'll start by explaining the various methods available for declaring variables, and then move on to describe how those methods effect JavaScript's behavior when compiling and executing code.  Variables, scope, and hoisting in JS can be confusing at first, especially if you've only been coding in Ruby up until this point, but with practice they can become second nature.

## The Basics

**Declaring a variable** just means telling the computer that the word you're about to put an equals sign after is a variable and that there are certain rules this variable should follow over the course if its lifespan.  In Ruby you don't need to do this; you can simply type ```variable_name = "value"```.  Technically, this *can* be done in JS, but the consequences of doing so, which we'll come to later on, are less desirable.

**Scope** refers to whether or not a variable is visible to the part of the program that is trying to access it.  There will be more on helpful ways to think about this concept later, but just know now that the 'global scope' is a kind of top level under which everything declared is visible everywhere else in the program.

**Hoisting** is something JavaScript does when it translates your code into something a computer can understand. The translation happens in two phases, a compilation phase and an execution phase, and hoisting occurs in the compilation phase (more on what all that means a bit later as well).

## Variables in the Global Scope

JS gives us three ways to declare a variable: ```var```, ```let```, and ```const```.  In the global scope, the main difference between ```var```, and the other two, is that ```var``` will not throw an error if you try to declare the same variable twice in the same scope, ie:

```
var fruit = "Apple";
//=> undefined
 
var fruit = "Orange";
//=> undefined
 
fruit;
//=> "Orange"
```

```let``` and ```const``` behave like this:

```
let fruit = "Apple";
//=> undefined
 
let fruit = "Orange";
//=> SyntaxError: Duplicate declaration "fruit" at ...
```

It might seem great at first to get fewer errors, but declaring a variable more than once in the same scope is usually a mistake and can be super confusing for those trying to read your code later on.  Receiving an error message is more useful here, as it helps to keep your code on point.  

* For this reason alone, it's probably best not to use ```var```.  Much of what ```var``` can do,```let``` is also capable of.  Furthermore, the scoping and hoisting issues with ```var``` will all but confirm these suspicions.

The basic difference between ```const``` and ```let``` is that ```let``` allows you to reassign an initialized variable, ie:

```
let fruit = "Orange";
//=> undefined

fruit;
//=> "Orange"

fruit = "Apple";
//=> "Apple"

fruit = 8;
//=> 8
```

whereas ```const``` will throw an error:

```
const fruit = "Orange";
//=> undefined

fruit;
//=> "Orange"

fruit = "Apple";
SyntaxError: Assignment to constant variable: fruit at...
```

Choosing to use one over the other depends on how you want the variable to function throughout the program; however, more often than not, ```const``` will be the variable of choice.  ```const``` gives us the ability to know exactly what's inside its variable at any given moment in the program.  ```let``` is useful when a variable's value will need to change (ie: a counter for a loop). 

## Scope (Function vs Block)

As from before, scope tells a program where a variable is visible in relation to other commands in the program.

Here's is a useful example from [You Don't Know Java Script by Kyle Simpson](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch1.md).  I use it because there is an accompanying image that really helps me to visualize scope.

Let's take this block of code which uses functions to create three nested scopes:

```
function foo(a) {

	var b = a * 2;

	function bar(c) {
		console.log( a, b, c );
	}

	bar(b * 3);
}

foo( 2 ); 
//=> 2 4 12
```

Simpson asks you to think of these scopes as bubbles inside each other.  Like so:

![](https://i.imgur.com/ECmhD19.png)

> Bubble 1 [Green] encompasses the global scope, and has just one identifier in it [a function]: foo.
> 
> Bubble 2 [Orange] encompasses the scope of foo, which includes the three identifiers: a [a parameter], bar[a function] and b [a variable].
> 
> Bubble 3 [Blue] encompasses the scope of bar, and it includes just one identifier: c [a parameter]. 

Looking at how the program above executes, notice that ```bar()``` is capable of manipulating ```a```, ```b```, and ```c```, while  ```foo()``` can only use ```a```, and ```b```, and the global scope only has access to ```foo()``` itself.

That example used functions to create differing levels of scope, but scope can also be created by blocks, as in an if statement:

```
const checker = true;

if (checker) {
	const drink = "milk";
	console.log( "got " + drink + "?");
	//=>got milk?
}

drink;
//=>ReferenceError: drink is not defined
```

or a for loop:

```
for (let i=0; i<10; i++) {
	console.log( i );
}
```

or anything that's not a function and gets stuck between two curly braces, {}.

```
{
  const hello = "world"
}

console.log(hello);
//=>ReferenceError: hello is not defined
```

## Using Var, Const, and Let in Relation to Scope

Now that we understand the basics of scope and variables in JavaScript let's mess around with them to see if we get anything funky or unexpected.

Let's take one of the block scope examples above and see if we can get some unexpected behavior:

```
const checker = true;

function advertisement(){
  if (checker) {
    const drink = "milk";
  }
  console.log( "got " + drink + "?");
}

advertisement()
```

All we've done here is put the block inside a function and move the log outside that block.  What would we expect when we call the function?  ... An error, of course!  A Reference Error to be more specific because what's declared inside the if statement block isn't visible to its parent scope, advertisement().

But what happens when we use var instead of const?

```
const checker = true;

function advertisement(){
  if (checker) {
    var drink = "milk";
  }
  console.log( "got " + drink + "?");
}

advertisement()
```

It outputs "got milk?" to the console.  

What about that for loop we made earlier?:

```
let num = 100;
for (let num = 1; num <= 3; num++) {
  console.log(num); 
	//=> 1 2 3
}
console.log(num); 
//=> 100
```

When we use let, we get what we expect.  Num equals ```100``` in the global scope and num equals ```1 2 3``` in the block scope.  Now try var:

```
var num = 100;
for (var num = 1; num <= 3; num++) {
  console.log(num); 
	//=> 1 2 3
}
console.log(num); 
//=> 4
```

What's going on here?  It turns out that in JavaScript ```var``` has what's called function scope and ```let``` and ```const``` have what's called block scope.  This inability of ```var``` to obey the laws of scoping when it comes to blocks is another quality that makes ```var``` less desirable to use.

## Lexical Scoping and Hoisting

There are different models for how scope can work, but the one JavaScript uses is called Lexical Scope.  This just means that the scope is defined by you at the time of you writing your code and does not change.  You can also think of it as 'static scoping' and in opposition to 'dynamic scoping,' which some other languages use.  Since scope doesn't change after you've written it in JS, the compilation phase of running your program goes through your code and associates all your declarations with the appropriate scope before execution.

Way back in the beginning of this blog post I mentioned how JavaScript goes through two phases in translating your code to computer behavior, a compilation phase and an execution phase.  During the compilation phase both variables and functions, are processed before any part of your code is executed.  Due to this two part process, the program will know what variables and functions you have declared before it knows to what they will become equal.  Consider this:

```
function myFn(){
  console.log(hoist);
  var hoist = "Hoist me!";
}

myFn();
```

If we run this code, what we might expect to get back is a Reference Error because we're trying to use a variable before it has been declared.  However, what we get is 'undefined', as in: the variable exists, but hasn't been defined a value yet.  It's tempting to think that ```var hoist = "Hoist me!";``` is all happening together on one line, but because of the way JS compiles it actually looks something like this during the execution phase:

```
function myFn(){
  var hoist 
  console.log(hoist);
  hoist = "Hoist me!";
}

myFn();
```

This is where term hoisting comes from.  JS 'hoists' the variable declaration to the top of its scope before it does anything else.

What happens if we use ```let``` or ```const``` instead of ```var```?

```
function myFn(){
  console.log(hoist);
  let hoist = "Hoist me!";
}

myFn();
```

We get a reference error!  What we expected from before.  This, again, is another reason why ```const``` and ```let``` are used over ```var```: they are not hoisted in the same way var is.  JS does still hoist them, but it does not initialize them. ```var```, as we have just seen, is initialized to a value of 'undefined'.

Not only does JS hoist variables, but it also hoists functions.

```
hello()

function hello(){
  console.log("Hello")
}
```

This example outputs "Hello" to the console because JS has already initialized the hello() function during the compilation phase.  But which does it hoist first, function or variables?  What would this code give us:
 
```
hello();

var hello;

function hello() {
	console.log( "Hello" );
}

hello = function() {
	console.log( "World" );
};
```

It gives us "Hello" because JS hoists functions first.  And what if we were to double-declare the function?

```
hello();

var hello;

function hello() {
	console.log( "Hello" );
}

hello = function() {
	console.log( "World" );
};'

function hello() {
	console.log( "World" );
}
```

We would get "World" in this instance because JS overrides previous functions with subsequent ones when it hoists.  Another case, since we know that [functions can also be expressions (as opposed to statements)](http://2ality.com/2012/09/expressions-vs-statements.html), occurs when we use functions as expressions:

```
hello();

var hello = function () {
	console.log( "Hello" );
}
```

Here we get the error "TypeError: hello is not a function".  This is because JS is hoisting the variable, but its assignment to a function is not being hoisted.  Lastly, what if we assign a variable and then declare it as a function?

```
var hello = "Hello"

function hello() {
	console.log( "World" );
}

console.log(typeof hello)
```

console.log here will tell us that hello is a string, not a function, which shows us that even though functions are declared before variables.  Variable assignment takes precedence over function declaration.

## Variable Assignments without Declarations

The last thing I want to touch on is what happens when a variable is assigned without using one of the declarations similar to how it's done in Ruby.  What might we expect from the following code?

```
fruit = "Apple";

var fruit;

console.log( "Apple );
```

One might think that we would get an error telling us the variable, fruit, has not been defined since we have not yet declared it; however, in JS when a variable is assigned without a declaration it automatically assigns the variable to the global scope.  This is called implicit declaration.

```
function myFn (){
  fruit = "Orange"
}

myFn();
console.log(fruit)
```

If ```fruit``` in the code above was declared with ```var```, ```let```, or ```const``` we'd get back "ReferenceError: fruit is not defined" because the variable would only exist within the scope of the function.  However, if you type in the code above you'll see that what actually pops out is "Orange".  Once myFn() was called ```fruit``` got defined.

