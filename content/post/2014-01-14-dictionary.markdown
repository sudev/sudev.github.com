---
categories:
- posts
comments: true
date: 2014-01-14T00:00:00Z
tags:
- dictionary
- linux
- terminal
- dictionary script
- script to find meaning
- bash
title: A dictionary in your terminal
---

Owning to my poor vocabulary I had to look up for meanings every now and then, the following script gets you the meaning for any word from dictionary.com using bash. 

### A bash script dictionary 
{{< highlight csv >}}
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
m=$(cat $file | grep description | grep -o 'content=.*.*See more' | grep -o '\,.*.\.')

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

{{< / highlight >}}

### A function to pronounce a word

{{< highlight csv >}}
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
m=$(cat $file | grep -o '//upload.*.ogg' | grep -v type)

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


{{< / highlight >}}
