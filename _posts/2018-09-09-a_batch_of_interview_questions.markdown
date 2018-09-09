---
layout: post
title:      "A Batch of Interview Questions"
date:       2018-09-09 15:05:20 -0400
permalink:  a_batch_of_interview_questions
---


I recently had an experience on my job search where I was asked to record an interview with no one on the other end of the video chat.  After speaking with a few people, I've come to find out this is more common than I'd originally thought and is just one of the many ways companies will use to screen potential candidates.  What follows are the questions I was asked, and an approximation of the answers I was able to muster.  

With mine, I think they were trying to assess whether or not it would be worth it to call me in based on a set of fundamental questions.  The job has to do with development using Javascript and CSS, so the questions are testing for this knowledge.  These answers are coming mainly from my experience with the Flatiron curriculum as I didn't have much experience in the field previous to taking classes with Flatiron.  I'm happy to report that everything I was asked was indeed covered in the Flatiron curriculum.  Lastly, the process lasted about 10-15 minutes; I was given 30 seconds to look at the questions and 30 seconds to answer.

### What is the difference between `==` and `===` in Javascript?

The short answer is that `==` compares value while `===` compares value and type.  When using `==` JS will run a type conversion to try and make the values comparable but `===` will not.  `==` is called an equality operator in JS and `===` is referred to as an identity operator.

### What is the difference when you define a variable using `var` as opposed to not using `var`?

If you're in the global scope there is no difference; however variables defined with `var` can be function scoped, meaning they will only be available within the lexical context of a particular function.  Variables that are defined without `var` will automatically be globally scoped regardless of their placement in any lexical context.  Defining variables without using `var`, `let`, or `const` is mostly accidental and can cause some weird and difficult to decipher debugging behavior.

### How do `let` and `const` differ from `var` in ES6?

First, `var` declarations are globally/function scoped while `let` and `const` declarations are block scoped.  `var` variables can be updated and re-declared within their scope; `let` variables can be updated but not re-declared; `const` variables can neither be updated nor re-declared.  They are all hoisted, but while `var` variables are initialized with `undefined`, `let` and `const` variables are not initialized.  Lastly, `var` and `let` can be declared without being initialized, but `const` must be initialized during declaration.

### What is the advantage of AJAX calls

The biggest advantages of AJAX are that it can reload only the content that needs to be reloaded on a page which reduces bandwidth/load time and improves user experience.  When you use AJAX is possible to make a webpage feel more interactive and quick to respond, as if it were an application running on your computer rather than a series of stateless requests made to a server through the internet.

### What is a closure in Javascript?

Closure refers to the way functions in Javascript carry around the lexical environment in which they were defined.  This becomes really powerful when you define a function inside of another function and you expose the nested function by either returning it or passing it to another function.  When the returned function gets called it will have access to the entire surrounding state that was available to it at the time of definition even though when it's called that state will most likely no longer be in the execution stack.  This is super useful for things like object data privacy, event handlers and callbacks, and partial applications and currying. 

### What is CRUD in REST and what are the corresponding HTTP operations?

CRUD is a cycle for keeping records current and permanent in a database setting.  Rest is an architecture for developing API using HTTP protocol to link resources to actions in a client-server relationship.  Each step in the CRUD cycle maps to a RESTful HTTP protocol: Create to POST, Read to GET, Update to PUT/PATCH, and Delete to DELETE.

### What is Event Bubbling?

Bubbling refers to the order in which event handlers are called when one HTML element is nested inside another element and both have registered a listener for the same event.  Events will registered starting with the innermost element and make their way to the outermost element or document level.  Bubbling is the default method of event propagation in most modern browsers, but another method is event capturing which will do the opposite of bubbling: events will be registered first at the topmost level and make their way down to the innermost.

### What is the Box Model?

The Box Model is how CSS treats every element in your HTML document: as a box with a bunch of properties that determine where it appears on the page.  At the core is the content measured by height/width properties, directly outside the content is padding, around the padding is a border, and around the border are margins.  Each of these layers of the box can be adjusted to create any number of spacing effects on any element on the page.

### What is Big O Notation?

Big O Notation is a tool for assessing algorithm efficiency.  It's a worst case scenario equation that shows how time or space scale with respect to some input variables.
