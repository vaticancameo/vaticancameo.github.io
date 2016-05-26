---
layout: post
title: "Generators in ES6"
categories:
excerpt: "New feature in javascript, what generators are and how to use them"
tags: [javascript, ES6, generators]
---

This is a very exciting update. One of the new things ES6 brings us is the use of generators. A generator is basically a function that can be paused. That's right, usually you can assumed your function will run to completion (with the exception of an error in an asynchronous callback), but generators can be paused and restarted with the introduction of the `yield` keyword. What does it look like?

{% highlight javascript %}
function *foo() {
  yield 1;
  yield 2;
  return 3;
}

//nothing happens
var bar = foo();
bar; //{[[GeneratorStatus]]: "suspended", [[GeneratorReceiver]]: Window}

console.log(bar.next()) //{value: 1, done: false}
console.log(bar.next())  //{value: 2, done: false}
console.log(bar.next())  //{value: 3, done: true}
console.log(bar.next())  //{value: undefined, done: true}

console.log(foo().next())  //{value: 1, done: false}
console.log(foo().next()) ) //{value: 1, done: false}
{% endhighlight %}

The `*` is used to indicate that a function is a generator, but you won't need to type it when calling the function. When we first run `foo()`, nothing happens, the function is immediately paused before the first `yield` keyword. When we try to see what bar is, `{[[GeneratorStatus]]: "suspended", [[GeneratorReceiver]]: Window}` is what will be printed to the console.

<br/>
Once paused, a generator will not restart unless you call it again. So if you left a generator, it'll forever be paused where you last called it. To restart a generator, call `.next()` on it, and it will run up till the next yield keyword, and return the value to the right of it.

<br/>
Calling `.next()` does not just give us a value, it gives us an object with its current value and the `done` status, making it clear whether a generator has run to completion or not. You can still use `return` with generators, however, it is typically a throw away value as the generator's done status will be 'true' after the last `yield`, so you would not get the returned value when using a `for..of` loop to iterate over the yields.

<br/>
As you can see, not assigning foo() to a variable and just calling next() on it will never make it go pass the first yield keyword.

#####Generators can also accept new values when called with next()

Here's what's even more cool about generators: it can be passed in new arguments when called with next(). Let's look at another example:

{% highlight javascript %}
function *baz(a) {
  var b = 'and' + (yield a);
  var c = 'is' + (yield b);
  return a + b + c;
}

var g = baz('one');

//not calling it with anything the first time
console.log(g.next()); //{value: "one", done: false}

console.log(g.next('two')); //{value: "andtwo", done: false}

console.log(g.next('three'));
//Object {value: "oneandtwoisthree", done: true}
{% endhighlight %}

Let's break this down:

* When we first assigned baz to g with "one", the generator is immediately paused, because the first things it evaluates is `yield a`, but it won't actually yield the value yet. **a is forever "one"** in this function.
* The next call to g gives us a value of "one", that makes sense because a is "one". We don't pass in a value because the first yield is not ready to accept a value yet, it has to send out whatever is to its right first. So if you pass it anything at this point, the value will be thrown away.
* Now we can pass in a value to the first yield expression, and the entire expression will become whatever we pass in. Calling g.next('two') will make b = "and" + "two", since we don't modify b any further in this function, **b is forever "andtwo"**.
* Then we pass in "three", but first b is yielded, giving us a value of "andtwo" like we expected. Then the `yield b` expression becomes "three", so **c is "isthree"**.
* Since there are no longer any yield expressions, the function will run to completion, giving us a returned value of "one" + "andtwo" + "isthree" = "oneandtwoisthree". That's it!
* **Note: you can pass in arguments as many times as there are yield keywords.**

<br/>Hope that clears up a little confusion around generators! For a more detailed discussion, I highly recommend this article: <http://davidwalsh.name/es6-generators>.
