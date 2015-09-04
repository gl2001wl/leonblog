---
layout: post
title:  "Set up blog by jekyll on github"
author:	"Leon Guo"
date:   2015-09-03 21:53:00
categories: website
tags: [github-page, jekyll]
---
### Download the resources you needed
Go to <http://rubyinstaller.org/downloads/> download ruby for using gem in cmd, you need download two packages:

+	Ruby 2.2.2 (x64)
+	DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe
	
Extract devkit to a directory, go into it and execute `ruby dk.rb init`, then open config.yml and add the home of ruby at the bottom, like '  - C:\Ruby22-x64'(you should use your ruby home).

Execute `ruby dk.rb install`

### Install
Because writer is in china, the F*** GFW stop me to visit rubygems, so I need to use an local image to instead of it
 {% highlight Bash %}
gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
 {% endhighlight %}
Then you can install jekyll
 {% highlight Bash %}
gem install jekyll
 {% endhighlight %}

### Set up your web site
Make a directory as the home of your web site, and then initialize jekyll in it

+	Make directory

 {% highlight Bash %}
mkdir leonblog
 {% endhighlight %}

+	Add git to it, create branch `gh-pages` and use this branch
 
 {% highlight Bash %}
cd leonblog
git init
git checkout  --orphan gh-pages
 {% endhighlight %}
 
+	Create a new repository in your github, I use `leonblog`

+	Initialize jekyll add commit it to github

 {% highlight Bash %}
jekyll new .
git add .
git commit -m "set up leon's blog"
git remote add origin https://github.com/gl2001wl/leonblog.git
git push origin gh-pages
{% endhighlight %}

### Verify
Visit <http://gl2001wl.github.io/leonblog/>
or execute `jekyll serv` and open <http://localhost:4000/leonblog> in your browser

### Issues
If there some display issues of the layout in github
	
*	open _config.yml file
	
*	change the baseurl, url and github_username to your real url in github
	
*	add "highlighter: pygments" and "layout: post"
	
*	open index.html file
	
*	change the layout to post

### Commit changes to github
 {% highlight Bash %}
git commit -am "comments of your changes"
git push origin gh-pages
 {% endhighlight %}

Refresh the web page, you can get the newest website.

### Add syntax highlight support
<http://jekyll-windows.juthilo.com/3-syntax-highlighting/>

### Use template made by others
There are so many existing jekyll template for you to use, you can find them here:
<http://jekyllthemes.org/>.

I'm using <https://github.com/bencentra/centrarium>.

### Publish your first article
You can post your article in folder `./_posts`, the name should be `YYYY-MM-DD-<The article name>.markdown`, so this article named `2015-09-03-set-up-blog-by-jekyllsite-on-github.markdown` for example.
Of cause you can use html to instead of markdown, but markdown is much sample and easy to write. You can find how to write markdown here:
<http://wowubuntu.com/markdown/>


