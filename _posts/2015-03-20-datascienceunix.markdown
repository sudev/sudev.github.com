---
layout: post
title: Data science and unix command line
category: posts
comments: true
tags: [unix command line, command line text manipulation, data science and unix commandline, data science large files, large dataset]
---

Note : *This article applies only to those who code*.
I have seen many people strugling with MS Excel trying to figure out data in a large csv file, I don't blame them beacause most people I have met ignore standard unix command line tools just because they never tried it. And if you are using Excel for large datasets it more like the donkey trying to pull too much load as shown below.   
<br />
![MS Excel for large dataset](/images/excel.jpg)    
<br />
When the data is BIG(anything above .5GBs) and if we are trying to figure out say even the coloumn names of a csv file MS Excel will get stuck and we will see a MS Windows `Not Responding`. And if we are going to script using python/R/perl/* we will spend some time to script and more time for the script to complete.   
<br />
Assume that we have a csv "car.csv" as shown below:
{% highlight bash %}
"","mpg","cyl","disp","hp","drat","wt","qsec","vs","am","gear","carb"
"Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
"Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
"Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
"Hornet 4 Drive",21.4,6,258,110,3.08,3.215,19.44,1,0,3,1
"Hornet Sportabout",18.7,8,360,175,3.15,3.44,17.02,0,0,3,2
"Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
"Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
"Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
{% endhighlight %}


Let's assume that we have a BIG csv with many coloumns and we are interested in the sum of 12th coloumn (carb) of the file. 

{% highlight bash %}
cat car.csv | awk -F "," '{ sum += $12 } END { printf "%.2f\n", sum}'
# 29.00
{% endhighlight %}
Here we have used the standard command line tools `cat` and `awk` and we don't even load the entire data into memory.     
The above line says:    

1. Using `cat` stream the contents into **stdout**
1. Pipe the stdout from `cat` to the next command `awk`.
1. With Awk 
	* `-F` parameter takes the deliminator that is used in big.csv.
	* once you specify the `-F` to deliminator all the fields will be available to you as { $1, $2 ... } 
	* `sum += $12` will increment the variable sum with 12th coloumn of each line.
	* use `printf` to format result.

Other useful commands: 
<br /> <br />
*Head and tail*    
If we want to get a sample data from BIG csv. Again using head or tail command we are not loading the entire file into memory, we are just reading them line by line.

{% highlight bash %}
# To get the first 3 lines of your csv 
head car.csv -n 3
# "","mpg","cyl","disp","hp","drat","wt","qsec","vs","am","gear","carb"
# "Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4

# Similarly for the last 5 lines 
tail car.csv -n 3 
# "Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
# "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
{% endhighlight %}
We can even save the sample to a separate file to view them using a editor or to run some script over the sample file. Using the redirect operator `>` in bash you can redirect the output to a file.
{% highlight bash %}
# first 100 lines into a new file first100.txt
head -n 100 big.csv > first100.txt
{% endhighlight %}

*Word count*   
`wc` command is used to get the number of line, words and bytes in your file
{% highlight bash %}
# number of lines, words and bytes
wc car.csv
#  11  25 602 car.csv

# to only output number of lines 
wc -l car.csv
# 11
{% endhighlight %}

*Grep*    
We wish to search for the line containing some text `Datsun` we can use grep to search and return those rows for you. Grep supports regular expression and hell lot of options.

{% highlight bash %}
# Search for text Datsun 
grep Datsun car.csv
# "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
# "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1

# subset data with only lines containing Datsun
grep Datsun car.csv > datsun.csv
{% endhighlight %}
*cut*  
Cut is used to cut a line into fields according to a given deliminator or number of characters or any pattern. 
{% highlight bash %}
# To cut your csv and show only first and third coloumn
cut -d "," -f 1,3 car.csv
# "","cyl"
# "Mazda RX4",6
# "Mazda RX4 Wag",6
# "Datsun 710",4
# "Hornet 4 Drive",6
# "Hornet Sportabout",8
# "Mazda RX4",6
# "Mazda RX4 Wag",6
# "Mazda RX4",6
# "Mazda RX4 Wag",6
# "Datsun 710",4

# You can cut by specifying start/end/number of characters 
# To print only first 3 characters 
cut -c-3 car.csv
# "","m
# "Mazd
# "Mazd
# "Dats
# "Horn
# "Horn
# "Mazd
# "Mazd
# "Mazd
# "Mazd
# "Dats
{% endhighlight %}

*Sed and Awk*    
These two commands are more of a programing language than being just commands. We use sed and Awk to find and replace, count, add, basically to manipulate data.
Sed/Awk again combined with some regular expression will let we to do most manipulations on a text file.
{% highlight bash %}
# Replace all occurence of "Mazda" to "Maa" in your data.
sed s/Mazda/Maa/g car.csv
# "","mpg","cyl","disp","hp","drat","wt","qsec","vs","am","gear","carb"
# "Maa RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Maa RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
# "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
# "Hornet 4 Drive",21.4,6,258,110,3.08,3.215,19.44,1,0,3,1
# "Hornet Sportabout",18.7,8,360,175,3.15,3.44,17.02,0,0,3,2
# "Maa RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Maa RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
# "Maa RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Maa RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
# "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
{% endhighlight %}
Sed can be used to clean your dataset, mostly we find our data with unwanted characters and which needs to be ignored. Say we wish to delete all the lines containing audi.
{% highlight bash %}
# Delete all the lines containing Mazda 
sed /Mazda/d car.csv > noMazda.csv
{% endhighlight %}
Read more about [Sed](https://en.wikipedia.org/wiki/Sed) and [Awk](https://en.wikipedia.org/wiki/Awk).     
<br />
*Sort and uniq*   
Using sort command will sort the csv treating each line as a single string, we can use sort to even sort based on a coloumn, numerical order or in reverse order. Uniq can be used to return only uniq rows or return the duplicated ones. 
<br />
Say you want to sort a csv according to coloumn 12 (carb), -k (key) specifies the coloumn to sorted, -nr specifies sort to sort in reverse numeric order and -t specifies the coloumn deliminator.
{% highlight bash %}
# if you want to sort the csv according to carb(Coloumn 12)
sort -k 12 -nr -t "," car.csv
# "Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
# "Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
# "Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
# "Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Hornet Sportabout",18.7,8,360,175,3.15,3.44,17.02,0,0,3,2
# "Hornet 4 Drive",21.4,6,258,110,3.08,3.215,19.44,1,0,3,1
# "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
# "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
# "","mpg","cyl","disp","hp","drat","wt","qsec","vs","am","gear","carb"
{% endhighlight %}
Use uniq command to output only the uniq rows, -c flag will also append the number occurences as the first coloumn for each row.
{% highlight bash %}
sort car.csv | uniq -c
# 2 "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
# 1 "Hornet 4 Drive",21.4,6,258,110,3.08,3.215,19.44,1,0,3,1
# 1 "Hornet Sportabout",18.7,8,360,175,3.15,3.44,17.02,0,0,3,2
# 3 "Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# 3 "Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
# 1 "","mpg","cyl","disp","hp","drat","wt","qsec","vs","am","gear","carb"
{% endhighlight %}
To see only the duplicate lines pass a -d flag.
{% highlight bash %}
sort car.csv | uniq -d
# "Datsun 710",22.8,4,108,93,3.85,2.32,18.61,1,1,4,1
# "Mazda RX4",21,6,160,110,3.9,2.62,16.46,0,1,4,4
# "Mazda RX4 Wag",21,6,160,110,3.9,2.875,17.02,0,1,4,4
{% endhighlight %}
To get total number of uniq lines pipe `uniq` output to `wc`.
{% highlight bash %}
sort car.csv | uniq | wc -l
6
{% endhighlight %}
*GNU split*    
Sometimes you just want to split the file into n small parts and run your script over these n part files.
{% highlight bash %}
#Split the csv into part files of 100 lines
split big.csv --lines 100
{% endhighlight %}
The file will be splitted into part  files as { xaa, xab ... } in the same folder.   
<br />
*GNU Plot*    
GNU plot is awesome tool for plotting. Read [more](http://gnuplot.sourceforge.net/demo_cvs/).     
<br />
I just want to say that working with unix command line tools is easier than using any other graphical tools, all you have to do is use them for once. There are many custom tools to ease your workflow like jsontoCsv, GNU plot etc.
*And you dont have to remember any of these parameters, use man page!*    
<br />
Links:

* [Using command line for datascience](http://jeroenjanssens.com/2013/09/19/seven-command-line-tools-for-data-science.html)
* [A beginners guide that I wrote with abijith for fosscell juniors](https://github.com/fosscell/bashworkshop)
* [Noufal Ibrahim's unix blog post](http://thelycaeum.in/blog/2013/09/03/text_processing_in_unix/)
* [I copied many examples from here](http://www.gregreda.com/2013/07/15/unix-commands-for-data-science/)

---



[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev
