---
layout: post
title: What kind of scopes do variables have
categories:
excerpt: "How scoping works in javascript"
tags: [javascript, variables, scope]
---

First of all, what is a scope? A scope is the part of the program where a binding between a variable and value is valid. This means you can call that variable at that place without getting an 'Undefined' or some other error.

<br/>
Javascript enforces lexical scoping, meaning that variables can only be called from within the block of code it is defined. Let's see some examples.

<br/>
If we're just writing variable in the program file, aka the top level, then those variables are in the global scope, meaning it can be accessed from anywhere in the file (sometimes even across files in some JS enviroments).

{% highlight javascript %}
var thing = 'a thing';
thing; //returns 'a thing'
{% endhighlight %}

When you write a new function, a new lexical scope is created, meaning that variables defined in that function that only be accessed from within that function. If/else blocks, for loops, or any other blocks that's not part of a new function don't create new lexical scopes.

{% highlight javascript %}
var makeNewThing = function() {
  var newThing = 'new thing';
  return newThing;
}

if (newThing) //will throw exception: "Uncaught ReferenceError: newThing is not defined"
{
  console.log(newThing);
}

if (thing) {
  console.log(thing); //'a thing'
}
{% endhighlight %}

However, inner functions have scope access to the outer functions in which they are defined, as well as the global scope. This is where it gets interesting.

{% highlight javascript %}
var printOne = function() {
  var one = 1;

  var printOneTwo = function() {
    var two = 2;

    var printOneTwoThree = function() {
      var three = 3;
      console.log(one);
      console.log(two);
      console.log(three);
    }

    console.log(one);
    console.log(two);;
  }

  console.log(one);
}
{% endhighlight %}

So as you can see, the most inner function have access to variables one, two and three, the second inner function only has acess to one and two, while the outer function does not have access to any of its inner function variables. And they all have access to thing variable, but not newThing if we're still in the same file.

<br/>
If you understand scoping, you're close to understanding closures, so check that out next!
