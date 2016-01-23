---
layout: post
title: "Communicating Intent: #1 - Ruby's Tap."
---
As software projects grow, being able to demonstrate your intention to other developers through clear and reasonable code is paramount. In this series I cover short tips to do just that.

Today we cover the under appreciated #tap method:

Library: [Ruby Core](http://ruby-doc.org/core-2.3.0/).
Class: [Object](http://ruby-doc.org/core-2.3.0/Object.html#method-i-tap).
Version: ruby > 1.9

### What does it do?
Ruby describes `#tap`'s behavior as "Yields self to the block, and then returns self". Put simply, it lets you act on an object while ensuring the object is also returned.

Example:
{% highlight ruby linenos %}
car = Car.create.tap do |c|
  c.wheels = 4
  c.move_to_showroom!
end
car.wheels #=> 4
{% endhighlight %}

### Why use it?
On the surface this looks useless. In almost all cases, you could just as easily do the following:

{% highlight ruby linenos %}
def make_car
  car = Car.create
  car.wheels = 4
  car.move_to_showroom!
end
{% endhighlight %}

Why should we ever use `#tap` then?

> Tap **should** be used to make the return value of a method explicitly clear.

For example, with the above `#make_car` method, imagine if the `#move_to_showroom!` method returned an instance of the car. This would mean the `#make_car` method also returns the newly created car. This is far from explicit in the code, a team member could quite easily add more lines to the `#make_car` method, changing the return value and breaking various parts of your application that have come to rely on `#make_car`.

This can be avoided, changing the method to `#tap` as below shouts **This method absolutely needs to return an instance of Car** from the proverbial rooftops.
{% highlight ruby linenos %}
def make_car
  Car.create.tap do |c|
    c.wheels = 4
    c.move_to_showroom!
    # In future, more lines can be added here without accidentally altering the return value of the method.
  end
end
{% endhighlight %}

Protecting your methods from accidental future sabotage can take many forms, but `#tap` can be a handy tool for your kit.
