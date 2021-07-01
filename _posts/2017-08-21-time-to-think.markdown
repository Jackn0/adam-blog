---
layout: post
title: Introduction
date: 2017-08-21 13:32:20 +0300
description: The foundation and model of Spiking neuron networks. # Add post description (optional)
img: post-5.jpg # Add image post (optional)
tags: [Blog, Meditation]
author: # Add name author (optional)
---
Spiking neuron networks (SNNs) are biologically inspired networks, which have received increasing attention due to their efficient computation. During their development, many mathematical models have been proposed to describe neuron behavior. The most widely used neuron model for SNN is the Leaky Integrate-and-Fire (LIF) model, which uses simple differential equations to describe the membrane potential  behavior of neurons. Its format of explicit iteration is governed by


$ v_{temp}(t+1)=v(t)+Ws(t) $

When the membrane potential exceeds the pre-defined threshold $V_{th}$, it would produce a spike output

$s'(t)=\begin{cases}
\theta & \text{ if } v_{temp}(t+1)\geq V_{th} \\ 
0 & \text{otherwise}
\end{cases}$

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

$ c = \sqrt{a^{2}+b_{xy}^{2} +e^{x}} $

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
