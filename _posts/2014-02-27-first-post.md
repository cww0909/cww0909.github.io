---
layout: post
title: First Post
categories: [guide]
tags: [jekyll, ruby, ubuntu, windows]
description: Setting up a Jekyll blog.
---

So I finally got this blog up and running, after more than a week of procrastinating and pondering on how I want things to be. I wanted to start a blog like this for a few months now, where I get to write about my encounters while programming as I tend to forget a lot of stuff after a few months and it would be nice to have an archive of all the things I learnt. Thus I came to the conclusion of writing a blog but without the constraints of a CMS like WordPress or Blogger, until I came across <a href="http://jekyllrb.com">Jekyll</a>.

Jekyll is really nice if all you want to do is host static websites, it's really lightweight and you don't need to login to a site to start writing posts. The layout is customisable and you can really do a lot of fancy stuff if you know enough HTML and CSS. Or you can go choose a theme that you like and use that, which is what I did. So as my first post, this will be a writeup on how my Jekyll blog was set up.

### Initial Environment
- Host OS: Windows 7 x64
- Virtual OS: Ubuntu 12.04.1 LTS
- Git (both host and virtual OS)

### What I Was Missing
It's nice to make changes and preview your site locally before pushing it for publishing, like `jekyll serve` and I can just preview the blog on my browser. To do this I needed: 
-Ruby
-RubyGems
Since Ruby is a pain to set up on Windows, I went with the easier route of setting up the initial draft of my blog in my virtual Ubuntu instead of spending time trying to force Ruby to run properly on Windows. If you're using Linux I don't recommend installing Ruby as a root user though, because you can potentially encounter problems when you want different version of Ruby for example. To solve this I used <a href="http://rvm.io">RVM</a> to keep my development environments properly separated and it is also good practice to do so. Another alternative to RVM is <a href="github.com/sstephenson/rbenv">rbenv</a>.

### Documentation Read
For the most part, these documentations are enough to get you through the essential steps you need to get the blog running:
- <a href="http://rvm.io/rvm/install">RVM install guide</a>
- <a href="http://pages.github.com">GitHub Pages</a>
- <a href="http://jekyllrb.com/docs/home/">Jekyll documentation</a>

### Modifications
So the default blog layout and colour scheme looks unappealing, what can you do? First, you can manually modify the files in `_includes` and `_layouts` folder and the default CSS file, which I tried for an hour and gave up on. Or, you can do a <a href="http://google.com/search?q=jekyll+themes">Google</a> search for some Jekyll themes and use/modify them to your liking. Another good place to look for inspiration is the list of sites using Jekyll in the official Jekyll <a href="http://github.com/jekyll/jekyll/wiki/Sites">GitHub</a>.

For my blog, I used the <a href="http://github.com/dbtek/dbyll">dbyll</a> theme with minor changes. I downloaded the <a href="http://github.com/dbtek/dbyll/archive/master.zip">zip</a> file containing the demo version of the theme and changed the `_config.yml` file appropriately for my blog. Then, I added an <a href="{{ site.BASE_PATH }}/{{ site.about_path }}">About</a> section to my blog by changing the `_includes/default.html` file and adding a variable pointing to the About page in `_config.yml`. I also changed the default syntax highlighting scheme to something that looked a little softer by replacing the `assets/resources/syntax/syntax.css` file.

### Things To Keep In Mind
One of the first things I tried changing in the theme was the profile picture of the blog. The default picture was something off <a href="http://www.gravatar.com/">Gravatar</a>, which you can change easily by signing up, uploading a picture and linking your profile in the `email_md5` field in `_config.yml`. The downside (or upside) of this is now your GitHub profile is set to this picture, and who knows what other sites you registered under that email have a different profile picture now. However, if you'd rather the picture come from your repository, be prepared to do some image resizing, cropping, and CSS to fit the theme.

Also when previewing the blog locally, remember that links to anything in your blog is referenced relative to `site.BASE_PATH` and not the the local copy, so don't be surprised if you get 404 for some new pages. What you can do is change all references `site.BASE_PATH` to `/` and solve this problem (not tested).
 
### To-Do
- `_drafts` folder for longer posts that need a few sessions to complete
- start posting links using the `[[text]](http://link-to-said-text.com/)` format instead of using HTML

### Additional Notes
I decided to set up Jekyll on Linux to preview any significant changes I'll make to my blog, and resort to just writing future posts in Windows and push it to the repo to publish. But if you still insist on using Windows then [[here]](https://github.com/juthilo/run-jekyll-on-windows/) is a good resource to look at to get Jekyll running on Windows.
