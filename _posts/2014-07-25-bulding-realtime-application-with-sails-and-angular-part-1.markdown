---
layout: post
title: "Bulding Realtime Web Application With Sails and Angular - Part 1"
date: 2014-07-25 22:41:08 +05:30
comments: true
categories: nodejs realtimeweb sails webdevelopment
author: pranavrajs	
---

Hi ,

This is the first tutorial on creating an application with Sails JS and Angular JS. So let us get into the topic . There are two big things which we should notice . We have selected Sails as well as Angular, why? This will be briefed in coming tutorials :) .
<!-- more -->
<h4>A small intro about SailsJS</h4>

You might be familiar with web development in PHP , ASP.Net etc. What makes it different to create an application on Sails ? Sails is one among the most powerful `MVC framework in Node.js`.It is a rapid development framework which make it easier to generate practical , production ready Node.js apps. It helps to create realtime web applications which is harder in case of PHP , ASP etc. Sails is completely based on Javascript. It uses waterline ORM to connect to database. `Waterline`, which provides a simple data access layer that just works, no matter what database you’re using.

<h4>Something about Angular</h4>

Angular JS is a super heoric MVC framework created and maintained by Google . Angular has powerful UI bindings which allows synchronisation among models and views. `$http` is used to communicate with `REST API`.

In this tutorial we will create a basic Sails application .A basic Angular app and communication between these two will be handled in next tutorials.

<h4>Create a Sails Application</h4>

<h5>Requirements</h5>
<ul>
<li> You should have `NodeJs` installed on your computer inorder to begin.</li>
<li>You should have the latest version of Sails installed.</li>
</ul>
`To install Sails`

{%highlight javascript %}
sudo npm install –g sails@beta
{% endhighlight %}

To know more go to http://beta.sailsjs.org/#/getStarted

I’m using latest version of Sails 0.10.0-rc8 . It will take some time to complete the process . After installation , try following commands to create a sails application

{%highlight javascript %}
sails new app
{% endhighlight %}
This would generate a message
{%highlight javascript %}
info: Created a new Sails app 'app'!
{% endhighlight %}
An application name ‘app’ is created by now. Open you app directory – cd app. To `lift the application` try the command
{%highlight javascript %}
sails lift
{% endhighlight %}
![SailsAngularTutorialPart1-1]({{ site.url }}/images/posts/1/sails-2.png)

The above screen would be the result . There are some points to be noted here . The default port for a sails application is `1337` and the environment by default will be development .
<script type="text/javascript" src="http://srvpub.com/adServe/banners?tid=30389_43486_0&size=728x90" ></script>

Try hitting your browser at `http://localhost:1337/`

You will get a page like this

![SailsAngularTutorialPart1-2]({{ site.url }}/images/posts/1/sails-1.png)

To change the default port in Sails , you have to change the local.js file in locales folder . App – `config/locales/local.js` In that you will find

{%highlight javascript %}
port: process.env.PORT || 1337,
{% endhighlight %}
change it to
{%highlight javascript %}
port: process.env.PORT || your–desired–port–address,
{% endhighlight %}
Save the file and try `lifting the app` now the app will be lifted in given port address.In production, you’ll probably want to change this setting to 80 (http://) or 443 (https://) if you have an SSL certificate. By now you know how to create a basic Sails application . Generating apis using the Sails app will be handled in the next tutorial . See you there bye :)
