---
layout: post
title:  "Ternary disappointments in LUA"
date:   2021-07-11 18:30:00 -0700
categories: LUA, programming
---
Just thought I'd leave myself a note to register this little issue I bumped into before it gets committed to long term memory and I don't think about it anymore.

I love ternary operators. They're a simple and yet elegant way to deal with a simple and yet recurring problem. I use it whenever and wherever it makes sense and, if I didn't also strive for code readability, I would probably use it more.
Heck, I was overjoyed when C# introduced [null-conditional and null-coalescing assignment operators][null-operators].

I love LUA. Among other items in my ever growing LUA appreciation list, I love the fact that I can return and assign multiple variables at once. There's no limit to how many values you can return and their type doesn't even need to be consistent.
It opens the possibility for some really neat code to get written and, like ternary operators, it feels pretty elegant to me when used correctly. 
Unfortunately, among other items in yet another ever growing list, there are many ways to use it incorrectly.

I don't love that I can't combine ternary operators and LUA's multi-assignment capabilities.

If you're already familiarized with the ternary format in LUA, consider the following:

{% highlight lua %}
local condition = true
local a, b = condition and 1, 2 or 3, 4
print (a, b)
{% endhighlight %}

That piece of code, not unexpectedly, outputs `1, 2`. Our problems however begin when we flip the condition to false.
Once `condition == false` our output becomes `false, 2` instead of the expected `3, 4`. Fun.
That is a consequence of LUA's *operator hierarchy*. 
Once condition is evaluated as false the `and` operator attempts to concatenate `false and 1` and then assigns the resulting value to `a`. Meanwhile `b` receives 2, which is the value following up the comma.
In a nutshell, `condition` is treated as a *value* to be assigned rather than a *ternary condition* for the assignment.

After a myriad of experiments with different parenthesis configurations and function calls, the piece of code that approximates the original intention the closest turned out to be:
{% highlight lua %}
local a, b = unpack(condition and {1, 2} or {3, 4})
{% endhighlight %}

In that case the two values are being returned as a table which gets unpacked and properly assigned once the ternary operator resolves itself.

Striving for code readability unfortunately prevents me from ever thinking that's an acceptable approach to a simple assignment operation.
Guess I'm sticking with a good ol'reliable if else combo for now.

[null-operators]: https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-coalescing-operator

