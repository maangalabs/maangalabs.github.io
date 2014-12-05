---
layout: post
title: "Generating REST API With Sails JS - Part 2"
date: 2014-07-26 21:07:25 +05:30
comments: true
categories: nodejs realtimeweb sails webdevelopment
author: pranavrajs  
---

Hi ,

This is the second part of building a realtime web application with Sails and Angular. The concept of REST API is important here. You can read more about REST API and RESTFUL web services <a target="_blank" href="http://www.restapitutorial.com/">here</a>.

<h4>How to generate a REST API with Sails JS ?</h4>

As i said earlier Sails is a rapid prototyping platform , API generation is faster and easier in Sails . 
<!-- more -->
{%highlight javascript %}
sails generate api employee
{%endhighlight%}

![SailsAngularTutorialPart2-1]({{ site.url }}/images/posts/2/api-1.png)

This generate an API which can search, paginate, sort, filter, create, destroy, update, and associate employees.
API is available at http://localhost:1337/employee .

Let us to look into the details of what is happened here when we execute this command. Sails actually generates a new Model “Employee” (file located in `app/api/models/Employee.js`) and a Controller file “EmployeeContoller” ( located in `app/api/controllers/EmployeeController.js`). File structure is shown below.


![SailsAngularTutorialPart2-2]({{ site.url }}/images/posts/2/api-2.png)

The contents of Model file generated is shown below .

{%highlight javascript %}

/**
* Employee.js
*
* @description :: TODO: You might write a short summary of how this model works and what it represents here.
* @docs        :: http://sailsjs.org/#!documentation/models
*/

module.exports = {

  attributes: {

  }
};
{% endhighlight %}

Models may be accessed from our controllers, policies, services, responses, tests, and in custom model methods. There are many built-in methods available on models, the most important of which are the query methods: find, create, update, and destroy.

 The `data-type in Sails`can any one of the following listed below. It can a string,text, int, float, date, datetime, boolean, binary, array, json.The `available validations` are empty, required, notEmpty, undefined, string, alpha, numeric, alphanumeric, email, url, urlish, ip, ipv4, ipv6, creditcard, uuid, uuidv3, uuidv4, int, integer, number, finite, decimal, float, falsey, truthy, null, notNull, boolean, array, date, hexadecimal, hexColor, lowercase, uppercase, after, before, is, regex, not, notRegex, equals, contains, notContains, len, in, notIn, max, min, minLength, maxLength

 So we will now create a simple Model `Employee` which contains three attibutes , name : STRING , empnum : INTEGER , emailid : STRING.

{%highlight javascript %}

/**
* Employee.js
*
* @description :: TODO: You might write a short summary of how this model works and what it represents here.
* @docs        :: http://sailsjs.org/#!documentation/models
*/

module.exports = {

  attributes: {
      
      name:{
        type:"string", 
        required:true,
        minLength: 2
      },
      empnum:{
        type:"string",
        required:true,
        unique: true
      },
      email:{
        type:"email",
        required:true,
        unique: true
      }
      
  }
};


{% endhighlight %}

This defines an employee Model . Let us start inserting values into it . In development mode Sails gives us flexibility to have auto-generated APIs. You can inserted values as 

{% highlight text %}
http://localhost:1337/employee/create?name=Pranav&empnum=123&email=pranavrajs@gmail.com
{% endhighlight %}

You will get a screen like this. 

![SailsAngularTutorialPart2-3]({{ site.url }}/images/posts/2/api-3.png)

You may try adding more employees here. 

<strong>NOTE: Be sure that the Sails app is lifted properly . :) </strong>

We will now try to generate an API which returns an employee with given employee number. Let us look into EmployeeController.js file now.

{% highlight javascript %}

/**
 * EmployeeController
 *
 * @description :: Server-side logic for managing employees
 * @help        :: See http://links.sailsjs.org/docs/controllers
 */

module.exports = {
  
};

{% endhighlight %}

File will be as shown as above. If we want to write an API say `employee/findEmployeebyempnum` , we just have to write a function inside Employee Controller namely findEmployeebyempnum. We will try one example.


{% highlight javascript %}

/**
 * EmployeeCont findEmployeebyEmpnum as above . req corresponds to request and res corresponds to response. req.param gives the parameters passed in request here we take id as parameter(This is to be noted since iroller
 *
 * @description :: Server-side logic for managing employees
 * @help        :: See http://links.sailsjs.org/docs/controllers
 */

module.exports = {
  
  findEmployeebyEmpnum:function(req,res)
  {
    var id = req.param('id');
    Employee.findOne({empnum:id})
        .exec(function(err,user){

          if(err)
            res.json({error:err});
          if(user === undefined)
            res.json({notFound:true});
          else
            res.json({notFound:false,userData:user});
        });
  }
};

{% endhighlight %}

Write a function `findEmployeebyEmpnum` as above . req corresponds to `request` and res corresponds to `response`. `req.param` gives the parameters passed in request here we take id as parameter (This is to be noted since it is the default name during api calls).FindOne is a function available by default . One important thing to note is that you can access a model with its name in any controller. Here we check if any error is occured and then API returns error data,otherwise it checks whether the data obtained during querying contains user. This is achieved by checking user with `undefined`.If no user is found, API `returns a JSON with notFound set to true`, otherwise it `returns a JSON with notFound set to false and defined userData`

Let us try the first API we wrote :)

Check with the employee num 123

![SailsAngularTutorialPart2-5]({{ site.url }}/images/posts/2/api-5.png)

Check with some other value

![SailsAngularTutorialPart2-4]({{ site.url }}/images/posts/2/api-4.png)



Hey !!! It works :) . We will try using these APIs with Angular JS in front-end to build a reatime app in next tutorials . See you there , bye :)


<script type="text/javascript">
    google_ad_client = "ca-pub-7831813631234919";
    google_ad_slot = "4442833672";
    google_ad_width = 728;
    google_ad_height = 90;
</script>
<!-- Maangalabs-2 -->
<script type="text/javascript"
src="//pagead2.googlesyndication.com/pagead/show_ads.js">
</script>