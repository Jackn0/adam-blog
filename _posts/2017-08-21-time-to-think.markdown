---
layout: post
title: Introduction
date: 2017-08-21 13:32:20 +0300
description: The foundation and model of Spiking neuron networks. # Add post description (optional)
img: post-5.jpg # Add image post (optional)
tags: [Blog, Meditation]
author: # Add name author (optional)
---
## SNN model
Spiking neuron networks (SNNs) are biologically inspired networks, which have received increasing attention due to their efficient computation. During their development, many mathematical models have been proposed to describe neuron behavior. The most widely used neuron model for SNN is the Leaky Integrate-and-Fire (LIF) model, which uses simple differential equations to describe the membrane potential  behavior of neurons. Its format of explicit iteration is governed by


$ v_{temp}(t+1)=\alpha*v(t)+Ws(t) $

where $v$ and $v_{temp}$ is the membrane potential, $\alpha$ means the decay factor, $W$ denotes the synapse weight, and $s$ denotes the spike input at time t.

When the membrane potential exceeds the pre-defined threshold $V_{th}$, it would produce a spike output $\theta$, and the membrane potential will decrease by two reset mechanisms:

soft reset: $v(t+1)=v_{temp}(t+1)-V_{th}$  
hard reset: $v(t+1)=v_{temp}(t+1)*(1-\theta)$

## works
The major bottleneck of the spiking neuron network is how to acquire a well-performance SNN, especially on a complex dataset, since directly using the backpropagation algorithm is not suitable for SNN.  
Currently, two ways can effectively obtain excellent SNNs: surrogate gradient and ANN to SNN. ANN to SNN means converting a well-performed source ANN to a target SNN. Surrogate gradient methods use a soft relaxed function to replace the hard step function and train SNN by BPTT.

### ANN to SNN
In this subsection, We use the soft reset and IF model ($\alpha=1$) to reduce the information loss between the source ANN and target SNN. Our approach is based on threshold-balancing method, which contain three steps:  
1. Train the source ANN that only contains Convolutional, average pooling, fully connected layer, and ReLU activation function. Record the maximum activation of each layer.
2. Copy the network parameter to target SNN and replace the ReLU function with the IF model. The threshold of each layer is setting to the maximum activation.
3. Run the target SNN with enough simulation length to achieve acceptable accuracy.  

The converted SNN needs thousands of simulation time to achieve the same accuracy as source ANN, which does not meet the high-efficiency characteristics. So our work is to explore how to obtain higher accuracy, and lower inference latency converted SNN.  

#### Layer-wise conversion error
We split the conversion error into clipping error and flooring error. When the source ANN activation is larger than $V_{th}$, the SNN neuron's output is smaller than the output of the source neuron (clipping). And the reason for the flooring error is that SNN can't send accurate values to the next layer. The threshold balancing method can eliminate the clipping error since it uses maximum activation as the threshold. However, it also will increase the flooring error, which can be reduced by increasing simulation length. [Here](https://openreview.net/forum?id=FZ1oTwcXchK), we first find an equation to approximate the target SNN's spike frequency:

$\bar{s}^{l+1} = V_{th}/T \cdot clip(\left \lfloor (T/V_{th}) \cdot W^l \bar{s}^l,0,T \right \rfloor)$

Though analyze the output error between the source ANN and converted SNN, we decompose the conversion error into the output error of each layer. As a result, we can make the converted SNN closer to the source ANN by simply reducing the output error of each layer. Here we propose a method to reduce the flooring error: only to increase the SNN's bias by $V_{th}/2T$.

{% highlight ruby %}
for l=1 to L do 
  SNN.layer[l].thresh<-ANN.layer[l].maximum_activation
  SNN.layer[l].weight<-ANN.layer[l].weight
  SNN.layer[l].bias<-ANN.layer[l].bias + SNN.layer[l].thresh / (2*T)
end for
{% endhighlight %}


#### Reduce the output error layer-by-layer  
The previous method does not rely on real data statistics. It is made by a strong assumption that activation is uniformly distributed. In fact, we can get a better bias increment by analyzing the distribution of some training samples. Here, we propose two methods to calibrate the SNN's bias and weight, respectively layer-by-layer.  
**Bias correction (BC).** In order to calibrate the bias, we first define a reduced mean function:  
$\mu_c(x)=\frac{1}{wh}\sum_{i=1}^{w}\sum_{j=1}^{h}x_{c,i,j}$  
where $w$,$h$ are the width and height of the feature-map, so $\mu_c(x)$ computes the spatial mean of the feature-map in each channel c. The spatial mean of conversion error can be written by:  
$\mu_c (e^l) = \mu_c (x^l)-\mu_c(\bar{s}^l)$

**Potential correction (PC).** Potential is similar to bias correction. In this method we can directly set $v^l (0)$ to $T \times e^l$ to calibrate the initial potential.


{% highlight ruby %}
for l=1 to L do
  Layer[l].Error = ANN.layer[l].output - SNN.layer[l].frequency
  SNN.layer[l].bias <- SNN.layer[l].bias + Layer[l].Error.mean(0).mean(2).mean(3) # bias correct
  SNN.layer[l].mem <- SNN.layer[l].mean +  Layer[l].Error.mean(0) # potential correct  
end for
{% endhighlight %}

**Weight calibration (WC).** The layer-wise conversion can be written as $e^l = x^l - \bar{s}^l$. Then we need to minimize the formulation $\min_{w^l} || e^l ||^2$ via stochastic gradient descent.


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
