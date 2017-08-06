---
layout: post
title: "The perpetually misunderstood bang(!)"
---

Today we cover the bang(!) symbol. In my opinion is one of the most common, yet misunderstood tactics for communicating intent to other developers. While subtle, it can actually be pretty useful when used consistently.

The first thing to remember is that the `!` in ruby, at the end of a method name, such as `save!` does nothing special on it's own. It forms part of the method name and it's only purpose is to communicate something else about what that method might do.


A quick google search will lead to a lot of incorrect information. For instance, [here](http://stackoverflow.com/a/612196), [here](http://stackoverflow.com/a/18801459), [here](http://stackoverflow.com/a/31598104), [here](http://stackoverflow.com/a/16301829), [here](http://stackoverflow.com/a/16301928), [here](http://stackoverflow.com/a/6208446), [here](http://stackoverflow.com/a/6208856), [here](http://stackoverflow.com/a/6208417), [here](http://stackoverflow.com/a/6260608), [here](http://stackoverflow.com/a/33923572), [here](http://qr.ae/RO9KE9) and [here](http://www.wellho.net/mouth/2893_Exclamation-marks-and-question-marks-on-ruby-method-names.html).


These all tell you that the bang symbol simply modifies the object in place. It certainly _does_ do this in some cases, but it is definitely _not_ what bang means. An example of this is `downcase` vs `downcase!`. The `downcase!` will modify the existing string, while `downcase` will return a new string.

This is useful, however, the logic falls down very quickly. For example, the `pop` method:
{% highlight ruby linenos %}
a = [1,2,3]
a.pop
a #=> [1,2]
{% endhighlight %}

`pop` modified the array, but has no `!`.

Another example is the `save/save!` methods in Rails. `save` returns `nil` on a validation error, while `save!` throws an error.

In the [Workflow](https://github.com/geekq/workflow) gem, you use `state!` to move to a particular state.

None of these methods have anything to do with modifying the object, so why are they using bang?

Matz - the creator of Ruby - has something to say on the issue:

> The bang (!) does not mean "destructive" nor lack of it mean non
destructive either.  The bang sign means "the bang version is more
dangerous than its non bang counterpart; handle with care".  Since
Ruby has a lot of "destructive" methods, if bang signs follow your
opinion, every Ruby program would be full of bangs, thus ugly. ~ matz.

[Source](https://www.ruby-forum.com/topic/176830#773946)

So there we go. Use bang when you have a method that has the potential to be more dangerous. I find it nice to leave a comment on any bang method, explaining the danger.

Some good use cases for the bang:

* Throwing an error on failure instead of returning nil.
* Forcing a fetch to bypass cache and recalculate.
* Any other method that has may cause side effects that may not be expected.

Bang can be a fantastic tool for letting other developers know to proceed with caution, so use it when it makes sense. When exploring code, take special care to look out for these methods and ensure they are being used correctly.
