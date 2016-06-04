---
layout: post
title: "Adipoli - New Octopress Theme"
date: 2014-08-07 22:59:50 +0530
comments: true
categories: webdevelopment octopressthemes
author: pranavrajs	
featured_image: /images/posts/5/theme-1.png
---

Hi all, 


I've been working with a new theme which gives a fresh look for the blog. Here is it . 

<h4>Adipoli - An Octopress theme</h4>

I've started the work from the `bootstrap-theme` repository and i've used the same code highlighter. This theme gives you a feel of wordpress blogs. We have inbuilt integration with twitter and github . Sharing Widgets are also added . You can configure <strong>feedburner</strong> which is a newbie here :) .

<!-- more -->

<h4>Installation Instructions</h4>

From your Octopress directory execute these instructions 

{% highlight text %}
git clone https://github.com/pranavrajs/adipoli-octopress-theme.git .themes/adipoli
rake install["adipoli"]
rake generate
rake preview
{% endhighlight %}

Copy the following to your `_config.yml` file if not present. 


{% highlight text %}

default_asides: [asides/recent_posts.html, asides/github.html, asides/delicious.html, asides/pinboard.html, asides/googleplus.html]

{% endhighlight %}

Inorder to configure the feedburner Subscriptions add the following line to your `_config.yml` file .

{% highlight text %}

#feedburner username
feedburner_username: yourfeedname

{% endhighlight %}

You can see the widget on the right side bar of the blog. I've used Font-awesome icons to spice up the website and i've added the Google+ badges at the bottom footer of the page . 

Feel free to use it .


<h4>Desktop View </h4>

![OctopressThemeAdipoli-1]({{ site.url }}/images/posts/5/theme-1.png)

<h4>Tablet View</h4>

![OctopressThemeAdipoli-2]({{ site.url }}/images/posts/5/theme-2.png)


<h4>Mobile View </h4>

![OctopressThemeAdipoli-3]({{ site.url }}/images/posts/5/theme-3.png)

You can download the theme from its Github Repository .

<a href="https://github.com/pranavrajs/adipoli-octopress-theme/archive/master.zip" class="btn btn-primary" >Download this theme</a>

<iframe src="http://ghbtns.com/github-btn.html?user=pranavrajs&repo=adipoli-octopress-theme&type=fork" allowtransparency="true" frameborder="0" scrolling="0" width="62" height="20"></iframe>

<iframe src="http://ghbtns.com/github-btn.html?user=pranavrajs&repo=adipoli-octopress-theme&type=watch" allowtransparency="true" frameborder="0" scrolling="0" width="62" height="20"></iframe>


