---
categories:
- posts
comments: true
date: 2016-07-09T00:00:00Z
title: Normalization, Standardization and Rescaling
---

*This is copy/paste of an interesting FAQ from faqs.org, I really loved reading the article and thought of reposting the same in a better formatted manner for readability.*      


   

Courtsey: [ftp://ftp.sas.com/pub/neural/FAQ.html](ftp://ftp.sas.com/pub/neural/FAQ.html)


## First, some definitions

### Rescaling

"Rescaling" a vector means to add or subtract a constant and then multiply or divide by a constant, as you would do to change the units of measurement of the data.     
For example, to convert a temperature from Celsius to Fahrenheit.

### Normalizing

"Normalizing" a vector most often means dividing by a `norm of the vector`, for example, to make the Euclidean length of the vector equal to one. 
In the NN literature, "normalizing" also often refers to rescaling by the minimum and range of the vector, to make all the elements lie between 0 and 1.

### Standardizing

"Standardizing" a vector most often means subtracting a measure of location and dividing by a measure of scale. For example, if the vector contains random values with a Gaussian distribution, you might subtract the mean and divide by the standard deviation, thereby obtaining a `standard normal` random variable with mean 0 and standard deviation 1.



However, all of the above terms are used more or less interchangeably depending on the customs within various fields. Since the FAQ maintainer is a statistician, he is going to use the term "standardize" because that is what he is accustomed to.

## Now the question is, should you do any of these things to your data? 

**The answer is, it depends.**

There is a common misconception that the inputs to a multilayer perceptron must be in the interval [0,1]. There is in fact no such requirement, although there often are benefits to standardizing the inputs as discussed
below. *But it is better to have the input values centered around zero, so scaling the inputs to the interval [0,1] is usually a bad choice.*
<br><br>
If your output activation function has a range of [0,1], then obviously you must ensure that the target values lie within that range. But it is generally better to choose an output activation function suited to the distribution of the targets than to force your data to conform to the output activation function. See "Why use activation functions?"
<br><br>
When using an output activation with a range of [0,1], some people prefer to rescale the targets to a range of [.1,.9]. I suspect that the popularity of this gimmick is due to the slowness of standard backprop. But using a target range of [.1,.9] for a classification task gives you incorrect posterior probability estimates. This gimmick is unnecessary if you use an efficient training algorithm (see "What are conjugate gradients, Levenberg-Marquardt, etc.?"), and it is also unnecessary to avoid overflow (see "How to avoid overflow in the logistic function?").
<br><br>
Now for some of the gory details: note that the training data form a matrix. Let's set up this matrix so that each case forms a row, and the inputs and target variables form columns. You could conceivably standardize the rows or the columns or both or various other things, and these different ways of choosing vectors to standardize will have quite different effects on  training.
<br><br>
Standardizing either input or target variables tends to make the training process better behaved by improving the numerical condition [ftp://ftp.sas.com/pub/neural/illcond/illcond.html](ftp://ftp.sas.com/pub/neural/illcond/illcond.html) of the optimization problem and ensuring that various default values involved in initialization and termination are appropriate. Standardizing targets can also affect the objective function.
<br><br>

> Standardization of cases should be approached with caution because it discards information. If that information is irrelevant, then standardizing cases can be quite helpful. If that information is important, then standardizing cases can be disastrous.


to be completed...

---

[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev
