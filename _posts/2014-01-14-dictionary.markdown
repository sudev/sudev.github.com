---
layout: post
title: A dictionary in your terminal  
category: posts
comments: true
tags: [Sudev Ambadi, Sudev,bash dictionary, dictionary, linux, terminal, dictionary script, script to find meaning, dictionary script, bash script   ]
---

Due to my poor vocabulary I always had to look for the meaning of English words from dictionary.com. So I wrote a bash script to fetch meanings from dictionary.com within my terminal.

### A bash script dictionary 
{% highlight bash %}
dict() {

#Creating a temp folder 
dir=~/.dict

#Check for the existence if not create one
[[ -d $dir ]] || mkdir $dir


#download respective file from dictionary dot com 
# -q => do it quietly ie nothing @ screen 
# -O save it as mean
wget -q -O $dir/mean wget http://dictionary.reference.com/browse/$1

#Please DONT hardcode the value, give it to variable
file=$dir/mean

#greping out result
m=$(cat $file | grep description | grep -Po 'content=.*.*See more' | grep -Po '\,.*.\.')

#saving the error code 
k=$(echo $?)

#echoing
echo "Meaning of the word "$1" is"$m

#checks if the word was actually available else throws an error
if [[ $k -gt 0 ]]; 
then 
    echo ".........oops, cant find word "$1;
    fi
     
}

{% endhighlight %}

### A function to pronounce a word

{% highlight bash %}
pron(){
#A very simple pronunciation tool l

#Creating a temp folder
dir=~/.dict

#Check for the existence if not create one
[[ -d $dir ]] || mkdir $dir


#download respective file from dictionary dot com
# -q => do it quietly ie nothing @ screen
# -O save it as mean
wget -q -O $dir/pron http://en.wiktionary.org/wiki/$1

#Please DONT hardcode the value, give it to variable and then use it
file=$dir/pron

#greping out result
m=$(cat $file | grep -Po '//upload.*.ogg' | grep -v type)

#saving the error code
k=$(echo $?)

#download the file
wget -q -O $dir/$1.ogg http:$m

ans="y"

#while loop
while [[ $ans =~ [yY].* ]]
do
    #mplayer is one of the default audio player in linux
    mplayer $dir/$1.ogg
                    
    echo "do you want me to play it again [y] / [n] ?"
    #read user input
    read ans
done

#remove the file from temp folder
rm $dir/$1.ogg

#checks if the word was actually available else throws an error
if [[ $k -gt 0 ]];
then
    echo ".........oops, cant find word "$1;
    fi

#Exit
echo -e "\n Exitting ...\n"

}


{% endhighlight %}
---
[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev
