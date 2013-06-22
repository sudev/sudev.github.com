---
layout: post
title: Useful bashrc functions  
category: posts
comments: true
---


I'm going to share some of my bashrc functions which saves me a lot of time.

::: Killer :::

This function helps you find a process using a keyword, you don't have to use ps aux along with grep and then kill the process by entering the pid instead use this function give it a keyword and will it help you in killing a process.

{% highlight bash %}


killer() { 
echo "I'm going to kill these process";
ps -ef | grep $1 | grep -v grep
echo "Can I ? [y]es [n]o ";
read ans;
if [[ $ans =~ "y" ]] ;
then 
    ps -ef | grep  $1  | grep -v grep | awk '{print $2}' | xargs kill -s TERM 
fi 
}


{% endhighlight %}

usage : killer chromium

You will given a list of application which has a name chromium and proceed with a yes or no.

[ Update  This is not so useful function anymore you may use in built pkill command to get the same result. ]

::: Search and cd combined :::

This function will help you to search and cd into a folder in a single step.

{% highlight bash %}

scd() {
    pathe=$(find ~ -name $1 -type d | head -n 1)
    cd $pathe
}

{% endhighlight %}

usuage : scd Music

Will take you to Music folder, regardless of your present working directory

Copy paste the above code into your bashrc and source it.


---
[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev
