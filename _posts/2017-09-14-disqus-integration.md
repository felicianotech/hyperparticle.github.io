---
layout: post
title: 'Integrating Disqus Comments into Jekyll'
---

![Disqus Logo](/public/img/disqus-logo.png)

It turns out integrating Disqus is quite easy, no matter the website. I was pleasantly surprised to see a Jekyll option in the Disqus admin page (though, the steps were pretty much identical to their universal code option).

I registered my website, plopped in Disqus's code to `post.html`, and it magically worked.

For future configurablility with Jekyll, I found [this blog post](https://sgeos.github.io/jekyll/disqus/2016/02/14/adding-disqus-to-a-jekyll-blog.html) useful. By moving the Disqus code to `disqus.html` and surrounding it with `% if page.comments % % endif %`, I could simply `% include disqus.html %` wherever I need it. To display the number of comments at the top of each post with a link to the bottom where the comments are, I use `page.url` and `page.id` to set the appropriate variables for Disqus.

And it's as simple as that.
