---
layout: post
title: "Configure database adapters in Sails"
date: 2014-08-10 11:33:49 +0530
comments: true
categories: sails nodejs webdevelopment
author: pranavrajs  
---

Hi all, 

Today we will deal with configuring database adapters in Sails . It is easy to configure a database with Sails as it gives custom adapters . We have models with us , only thing we need to do is to create an adapter for MySQL,PostgreSQl, or mongoDB which ever we use . Here we will deal with configuring the adapter for MySQL.

<!-- more -->
<h4>Database Configuration</h4>

Before we start with adapters , we can configure the database for MySQL as follows .

I've my MySQL database with `username : root and password: root` .             

![ConfigureDBAdaptersinSails-1]({{ site.url }}/images/posts/6/adap-1.png)

Create a database as follows.

{% highlight text %}
CREATE DATABASE maangatuts;
{% endhighlight %}

To use that database execute the following command. 

{% highlight text %}
USE maangatuts;
{% endhighlight %}

Once you do this the result will be as seen below. 

![ConfigureDBAdaptersinSails-2]({{ site.url }}/images/posts/6/adap-2.png)

<h4>Configure adapters in Sails</h4>
Now you can configure your adapter. For that open the file `connections.js` located in `app/config/connections.js`. You might have a common doubt that even before configuring the database we were able to use the apis and data were stored accordingly . How is this possible ? This is because sails use `Waterline` which has a localDisk storage which is provided to minimize efforts during development . On `production` you have to configure it with a database.

Here we use MySQL as storage database. On `connections.js` file you can see the following lines. 
{% highlight javascript %}
  someMysqlServer: {
    adapter: 'sails-mysql',
    host: 'YOUR_MYSQL_SERVER_HOSTNAME_OR_IP_ADDRESS',
    user: 'YOUR_MYSQL_USER',
    password: 'YOUR_MYSQL_PASSWORD',
    database: 'YOUR_MYSQL_DB'
  },
{% endhighlight %}

Change this to your configuration as follows.

{% highlight javascript %}
  localhostMysqlServer: {
    adapter: 'sails-mysql',
    host: 'localhost',
    user: 'root',
    password: 'root',
    database: 'maangatuts'
  },
{% endhighlight %}

Initial configuration for MySQL adapter is now complete . Now you have to go back to the models and configure each model with the adapter.
We will now configure this adapter in our `Employee` model (app/api/models/Employee.js).

Add the following line to the file .

{% highlight javascript %}
connection: 'localMysqlServer',
{% endhighlight %}

The file now looks like this. 

{% highlight javascript %}

/**
* Employee.js
*
* @description :: TODO: You might write a short summary of how this model works and what it represents here.
* @docs        :: http://sailsjs.org/#!documentation/models
*/

module.exports = {
  connection: 'localMysqlServer',
  attributes: {
  		
  		name:{
  			type:"string",
  			required:true,
		    minLength: 2
  		},
  		empnum:{
  			type:"string",
  			required:true
  		},
  		email:{
  			type:"email",
  			required:"true",
  			unique: true
  		}
  		
  }
};


{% endhighlight %}

Now try to lift the app . You may get an error as shown below.

![ConfigureDBAdaptersinSails-3]({{ site.url }}/images/posts/6/adap-3.png)

This is because the `sails-mysql` adapter is not installed on your app.Install it by typing..

{% highlight text %}
npm install sails-mysql@0.10.x
{% endhighlight %}

Now try lifting the app . The app will be lifted properly . Check the employees present there. You will get a null value there . See the screenshot below.

![ConfigureDBAdaptersinSails-4]({{ site.url }}/images/posts/6/adap-4.png)

Now check the database whether the model is created as a table or not .

![ConfigureDBAdaptersinSails-5]({{ site.url }}/images/posts/6/adap-5.png)

If you get a screen as shown above then the connection is done successfully. Now try adding employees. 

![ConfigureDBAdaptersinSails-6]({{ site.url }}/images/posts/6/adap-6.png)

Check whether it is updated in Database you have configured.

![ConfigureDBAdaptersinSails-7]({{ site.url }}/images/posts/6/adap-7.png)

Yay! It works . You can add multiple adapters also. If you have two MySQL databases, you can try adding the code shown below.
{% highlight javascript %}
module.exports.connections = {
  localMysql: {
    adapter: 'sails-mysql',
    user: 'root',
    host: 'localhost',
    database: 'someDbase'
  },
  remoteMysql: {
    adapter: 'sails-mysql',
    user: 'remoteUser',
    password: 'remotePassword',
    host: 'http://remote-mysql-host.com',
    database: 'remoteDbase'
  }
};
{% endhighlight %}

<strong>Note :</strong>If any connection to an adapter is used by a model, then all connections to that adapter will be loaded on sails.lift, whether or not models are actually using them. It is therefore good practice to comment out any connection configurations that you aren't using!

Hey! that marks the end of this tutorial . We'll meet in the next tutorial in Sails-Angular Series. Bye then :) 

