---
layout: post
title: Offline mirror a website using wget 
category: posts
---

Use the following wget command to mirror a website 

{% highlight bash %} 
wget -mkpb some-website-url
{% endhighlight %}

-m : mirrors the entire website

-k : converts all links to suitable web viewing.

-p : downloads all required files like that of the css, js ...

-b :wget will run in background 

---



[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev
