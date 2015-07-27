---
layout: post
title: A recursion top down problem 
category: posts
comments: true
---

A top down recursion problem that helped me to understand the recursion other than using it in usual fibnacci and factorial.This problem and its expalnation is the same as given in Gayle Laakmann McDowell. 

###Problem Statement :
A child is running up a staircase with n steps, he can hop either 1 step, 2 step, or 3 steps at a time. Implement a method to count how many possible ways in which the child can run the stairs.

Now if you think you can implement without reading the solution go ahead, you dont have to read what is written down here :)

else the solution in here uses the recurion and the dynamic programming technique to an extend such that Im lovin it!

###Solution 

** Aprroach 1: **

Use recursion, ie count the no of ways considering the kids last hop and the decrement the no of steps recursively such that the resulting no of calls resulting with a zero will give you the total no of steps


{% highlight bash %}
int ways(inti k)
{
    if ( k == 0 ) {
        // This means we have reached the first step properly starting from the last one 
        return 1;
    }

    if( k < 0 ) {
        //This one wont yeild a perfect way since we took some miss steps during the course and we are not able to reach the start position properly
        return 0;
    }
    count = ways(k-1) + ways(k-2) + ways(k-3);
    //gives the total count considering the last to e either of the three and then doing it recursively
    return count;
}

{% endhighlight %}

Problems with this approach, just like the fibonacci example the time complexity of this problem is really huge exponential O(3 N). 
:( Not good, lets think of memoization now, what about saving the values into a array ?

{% highlight bash %}
{% endhighlight %}




---



[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev
