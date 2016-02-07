---
layout: post
title: "Very quick tips: Grepping rails routes."
---
While most rails developers work off either Mac OS X or Linux, extensive knowledge of various linux commands is definitely not required to be a proficient rails developer. In todays quick tip, I am going to show you an incredibly simple tip that can be a real time saver.

As you know, `rake routes` (also `rails routes` in rails 5) prints a list of all routes in your application - simple enough. As a project grows, this can quickly become hundreds of lines of output, especially after adding a few engines. This can make finding the actual route you are looking for quite difficult.

The fix:

{% highlight ruby linenos %}
rake routes | grep KEYWORD
{% endhighlight %}

This will filter the output of the lines to match your keyword. Add the name of the controller as the keyword, and you will just get routes related to that controller.

Super simple, but I find it really useful.
