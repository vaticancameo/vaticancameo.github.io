---
layout: post
title: How to do class inheritance in Javascript
categories:
excerpt: "functional and pseudoclassical inheritance"
tags: [javascript, inheritance, class]

---

There are two ways to do this, Functional and Pseudoclassical

#### In the functional style:

{% highlight javascript %}
var Animal = function() {
  var animal = {alive: true};
  animal.isAlive = function() {
    console.log(animal.alive);
  };
  animal.dies = function() {
    animal.alive = false;
  };
  return animal;
}


var Cat = function() {
  var cat = Animal();
  cat.meow = function() {
    console.log("meow");
  }
  return cat;
}


var Sesame = Cat();
Sesame.isAlive(); //true
Sesame.meow(); //"meow"
{% endhighlight %}

Okay, that was a bit morbid, but you can see how easy it is for objects to inherit the properties of its parent class in the functional style.

#### In the pseudoclassical style:

{% highlight javascript %}
var Animal = function() {
  this.alive = true;
};

Animal.prototype.isAlive = function() {
  console.log(this.alive);
};
//we'll skip the 'dies' property in this one


var Cat = function() {
  Animal.call(this);

  //if your object needs to be passed in initial arguments:
  //use Animal.call(this, arg) for one argument
  //and Animal.apply(this, args) for an array of arguments
  //more on that in another blog post
}

Cat.prototype = Object.create(Animal.prototype);

//because we overwrote the entire prototype object on Cat
//we lost the constructor property that comes with every object:
Cat.prototype.constructor = Cat;

Cat.prototype.meow = function() {
  console.log("meow");
}


var Sesame = Cat();
Sesame.isAlive(); //true
Sesame.meow(); //"meow"
{% endhighlight %}

And that's it! It looks more complicated, but it has one huge advantage over the functional style. In the functional style, every instance of the object has its own copy of all of the methods, and if there's a lot of methods and hundreds of instances, it will be terribly space inefficient, whereas in the pseudoclassical style, there's only one copy of each method on its prototype, and property lookup for instances will fall through to the original constructor's prototype in stead of referencing its own copy of that method.
