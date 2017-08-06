---
layout: post
title: "Very quick tip: Grepping rails routes."
---

As you probably know, `rake routes` (also `rails routes` in rails 5) prints a list of all routes in your application - simple enough. As a project grows however, the `routes.rb` file can get pretty large fairly quickly. This can make finding the actual route you are looking for tough, especially after adding a few engines.

The fix:

{% highlight ruby linenos %}
rake routes | grep KEYWORD
{% endhighlight %}

This will filter the output of the lines to match your keyword.

To return all routes related to a controller, simply add the name of the controller:
{% highlight ruby linenos %}
rake routes | grep users
{ endhighlight }

To return all routes with a specific HTTP Verb, simply add GET, POST, PATCH:
{% highlight ruby linenos %}
rake routes | grep GET
{ endhighlight }

To return all routes related to an action:
{% highlight ruby linenos %}
rake routes | grep update
{ endhighlight }

Super simple, but I find it really useful.
