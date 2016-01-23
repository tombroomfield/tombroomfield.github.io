# Showing Intent: #1 - Tap
As a software project grows, being able to demonstrate your intent to other developers through clear and readable code is paramount. In this series I cover short tips to do just that.

Today we cover the under appreciated #tap method:

** Library: ** [Ruby Core](http://ruby-doc.org/core-2.3.0/)
** Class: ** [Object](http://ruby-doc.org/core-2.3.0/Object.html#method-i-tap)
** Version: ** [ > 1.9]()


### What does it do?
Quite simply, it will yield a block with a reference to itself and then also, return itself.

Example:
``` ruby
car = Car.create.tap do |c|
  c.wheels = 4
  c.move_to_showroom!
end
```

### Why use it?
The `#tap` method can seem fairly useless. In almost all cases, you could just as easily not use it. For instance, the above code could also read:

``` ruby
def make_car
  car = Car.create
  car.wheels = 4
  car.move_to_showroom!
end
```

Why should we ever use `#tap` then? It **should** be used to make the return value of a method explicitly clear.

For example, with the above `#make_car` method, imagine if the `#move_to_showroom!` method returned an instance of the car. This would mean the `#make_car` method also returns the newly created car. This is far from explicit in the code, a team member could quite easily add more lines to the `#make_car` method, changing the return value and breaking various parts of your application that have come to rely on `#make_car` actually retuning the newly created car.

This can be avoided, changing the method to `#tap` as below shouts **This method absolutely needs to return an instance of Car** from the proverbial rooftops.
``` ruby
def make_car
  Car.create.tap do |c|
    c.wheels = 4
    c.move_to_showroom!
    # In future, more lines can be added here without accidentally altering the return value of the method.
  end
end
```
