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

Now you may be wondering the reason for me using the primitive style of saving result into a variable and then cd'ing into it.
I tried using exec with find, but the exec expects a executable binary (something of the sort /bin/bash ) and cd is a shell bultin,which means you will have to leave the parent shell to cd into folder the desired folder and the one liner for the same will look something of this sort.

{% highlight bash %}
find ~ -name $1 -type d -exec bash -c "cd '{}'; exec bash" \;
{% endhighlight %}

Also note that you will taken into a new shell and changes that you make in the new shell will not be reflected back in the parent shell, and its a pain to do exit each and every shell you login.
You can have a look at my [stackoverflow post](http://stackoverflow.com/questions/17248568/a-shell-script-to-find-and-cd-into-a-folder-taking-a-folder-name-as-argument-in) regarding the doubts that I had with this implementation.
If you have any suggestion to improve the thing please let me know through a comment.

usuage : scd Music

Will take you to Music folder, regardless of your present working directory

Copy paste the above code into your bashrc and source it.


---
[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev
