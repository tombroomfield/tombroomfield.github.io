---
layout: post
title: Sandi Metz doesn't hate you - Part 1: 100 Lines of disorganization and disrepute to delegation and delightful code.
---

The point of this post: Each of Sandi Metz's rules has a specific lesson to teach. Today we look at classes with more than 100 lines of code.

[Sandi Metz](http://www.sandimetz.com/) is a prominent Rubyist, author and speaker from Durham, NC. Of all the topics she has covered on her [Blog](http://www.sandimetz.com/blog/) and in her incredible [Book](http://www.poodr.com/), the concept that has resonated with me most has been her 'Rules for developers'.

Apparently I am not alone in this. Hugo, our resident [Hubot](https://hubot.github.com/), even knows them by heart.

Take it away Hugo:
![The rules, by Hugo, our Hubot. Your class can be no longer than 100 lines of code. Your methods can be no longer than 5 lines of code. You can pass no more than 4 parameters and you can't just make it one big hash. In your rails controller, you can only instantiate 1 object to do whatever it is that needs to be done. Your rails view can only know about 1 instance variable. You can break these rules if you can explain to your pair or in your pull request why it makes sense.](/images/the-rules.png)


My first reaction to the rules was fairly negative. It seems counterproductive to design code in order to satisfy six requirements. Should I spend 40 minutes finding a nice way to drop a line from a 6 line method? Why is a 90 line class fine, but 105 lines violates the rules? The rules initially seem strict, confining and result in more work without much gain.

Alas, I began implementing the rules in my code and very quickly began to realise what Sandi may be getting at.

Each 'Rule' was a cleverly disguised lesson, designed to teach an important OOP technique. Today we will break down Rule 1 and see if we can determine what there is to learn.

#####1) Your Class can be no longer than 100 lines of code.

The Lesson: Your class is doing to much, don't be afraid to implement other objects in order to maintain the 'Single Responsibility Principle'.

For whatever reason, new Rails developers have the impression that every class they create must either be a Model or a Controller. This is the not the case. It is perfectly acceptable and encouraged to create a PORO(Plain old ruby object) in order to abstract and organize similar logic. There are a few great tools and techniques to help in this process.

In a rails project, I generally find that my models threaten to violate this rule, and in most cases, user.rb reaches the 100 line mark first. The User model is generally dealing with validations, sessions, authorization, authentication and plethora of associations. This is a clear violation of the single responsibility principle and you have some serious refactoring to do.

Imagine we have a monolithic user class with 3 methods that all deal with Authentication. I am aware that there are a lots of gems that can provide this functionality, this is simply for the sake of example.

{% highlight ruby linenos %}
class User < ActiveRecord::Base
  # ... 100 lines of mess.

  def admin?
    roles.include?(:admin)
  end

  def author?
    roles.include?(:author)
  end

  def moderator?
    roles.include?(:moderator)
  end
end
{% endhighlight %}

All of these methods are very similar and should be abstracted appropriately.

Lets start by creating a PORO.

{% highlight ruby linenos %}
class AuthManager
  attr_accessor :user

  def initialize(user)
    self.user = user
  end

  def admin?
    user.roles.include?(:admin)
  end

  # ... author? and moderator? methods.
end
{% endhighlight %}

Great, now lets create a reference to our AuthManager from the user class.

{% highlight ruby linenos %}
class User
  # ... 100 lines of mess
  def auth_manager
    @auth_manager ||= AuthManager.new(self)
  end
end
{% endhighlight %}

And delegate our three methods to this our auth manager.

{% highlight ruby linenos %}
class User
  delegate :admin?, :author?, :moderator?, to: :auth_manager

  # ... now slightly less mess.
end

{% endhighlight %}

It is all pretty straightforward.

With this delegation in place, our user object still responds to the admin?, author? and moderator? methods, keeping the user api exactly the same.

{% highlight ruby linenos %}
User.new.admin? # => false
{% endhighlight %}

If you are not using Rails, a similar trick can be used by including the [Forwardable Module](http://ruby-doc.org//stdlib-2.0/libdoc/forwardable/rdoc/Forwardable.html) which is included in the Standard Library.

This will lead to far more manageable code and help you limit the size of your classes. A similar trick can be used by including modules, which I will cover in a later post.


Wrapping up, the 100 line rule is a lot less about the length of your files and a lot more about ensuring you are using proper OOP techniques to distribute responsibilities into seperate classes.

Thanks for reading, follow me on [twitter](https://twitter.com/tom_broomfield) for updates. Next time we tackle the 5 line method rule.

##Other Posts by Tom Broomfield.
[The upcoming ActiveRecord or method will clean up your queries](http://tombroomfield.com/the-upcoming-active-record-or-method-will-clean-up-your-queries/)
