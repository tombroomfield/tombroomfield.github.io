---
layout: post
title: The upcoming ActiveRecord or method will clean up your queries.
---

ActiveRecord is, in my opinion, the best implementation of an ORM I have ever had the pleasure to work with. However, all to often I find myself having to drop into Arel or straight up SQL in order to get what I need.

Often, this can be a code smell and can be good opportunity to review the structure of my data model. However, all too often more complex queries are necessary.

{% highlight ruby linenos %}
  User.where('first_name = ? OR first_name = ?', 'Tom', 'Rachel')
{% endhighlight %}

I find this quite jarring to be honest. When well written, Ruby is renowned for it's readability and usually Rails reflects this. On the other hand, SQL, is and does not.

This can usually be wrapped in a scope to pretty things up a bit.

{% highlight ruby linenos %}
class User
  scope :could_be_a_broomfield -> { User.where('first_name = ? OR first_name = ?', 'Tom', 'Rachel') }
end
{% endhighlight %}

This works, but it is unfortunate to see the model filled with scopes that may only be used once or twice, there are ways to deal with this as well, but I find it far from ideal. Queries should show intent.

ActiveRecord took a nice step forward with the introduction of the #not method in Rails 4. And they have continued in that direction with the recently approved #or method. Multiple pull requests have been opened and closed in recent history attempting to get this functionality in. [Here](https://github.com/rails/rails/pull/18706) and [here](https://github.com/rails/rails/pull/10891) for example. However, too confusion existed in the proposed implementations, especially around where the 'or' actually takes place in the method chain.

Recently though, [a much stricter implementation was recently proposed](https://github.com/rails/rails/pull/16052), and with the blessing of DHH himself, has been approved.

So, how does it work?

Well, when used simple it reads very nicely, our earlier query could be adapted with the following:

{% highlight ruby linenos %}
User.where(first_name: 'Tom').or(User.where(first_name: 'Rachel'))
# 'Tom' || 'Rachel'
{% endhighlight %}

It is important at this stage, to remember that the default #where query, when chained, acts as an AND.

So what happens when we do this?

{% highlight ruby linenos %}
User.where(country: 'Australia').where(first_name: 'Tom').or(User.where(first_name: 'Rachel'))
{% endhighlight %}

Would it:

A) Give me all Users who live in Australia, who either have the name Tom or Rachel.

B) Give me All users who live and Australia and are called Tom, or are called Rachel(And could be from any country).

The answer is A, which could be a source of confusion, in order to get all users in Australia, who are either called Tom or Rachel, we would have to do the following:

{% highlight ruby linenos %}
User.where(country: 'Australia').or(User.where(first_name: 'Tom')).where(first_name: 'Rachel')
{% endhighlight %}

It might take awhile to wrap your head around this method. It feels slightly clunky to me passing entire model query in to the #or method instead of an attribute hash. Long chains of #where and #or methods are going to be confusing and unreadable, so remember to keep using your scopes when you need them.

With all that said, I think this will be a welcome addition to the powerful tool belt that is ActiveRecord. I am very excited for the next Rails release so I can have a play. And in the end, any day you get to remove raw SQL from your Rails app is a day worth waking up for.
