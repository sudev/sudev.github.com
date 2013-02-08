---
layout: post
title: Offline mirror a website using wget 
category: posts
---
{ % highlight bash % }
wget --mirror --limit-rate=100k --wait=1 -erobots=off --no-parent --page-requisites --convert-links --no-host-directories --cut-dirs=2 --directory-prefix=OUTPUT_DIR http://www.example.org/dir1/dir2/index.html
{% endhighlight % }


---



[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev
