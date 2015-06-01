---
layout: post
title: "Using Angular JS with Sails Js REST APIs - Part 3"
date: 2014-08-01 21:26:02 +0530
comments: true
categories: nodejs webdevelopment sails realtimeweb angularjs
author: pranavrajs	
---

Hi all,

This is the third part of tutorial series - building realtime web application with Sails Js and Angular Js. In previous posts we have seen the methods to create REST APIs and create a Sails application. Keeping that in mind we can move on to next topic - Integrating Angular JS with Sails REST APIs.

<!-- more -->
Sails JS provides EJS templating engine . Many of you folks might not be aware of EJS , so we will do another approach , keeping Sails,only for REST API and building a front-end with Angular JS which communicate with these APIs. To make the point clear,look at the figure below.

![SailsAngularTutorialPart3-1]({{ site.url }}/images/posts/3/arc-1.png)

Architecture of the system consists of a server consisting of APIs only and a UI built in Angular. UI communicates with the server through API calls. The advantage of this system is that these APIs can be re-used when you create a mobile app or any other cloud app.

So let's get started. In this tutorial we will create a basic Angular app which communicates with Sails server. 

<h4>Angular App Architecture</h4>

Angular has very powerful data bindings and it is an MVC framework.Whenever the model changes, Angular refreshes the appropriate binding points, which updates the view.

A basic structure of an Angular app - HTML Markup is as follows


![SailsAngularTutorialPart3-2]({{ site.url }}/images/posts/3/arc-2.png)


<ul>
	<li><strong>ng-app </strong>: Use this directive to auto-bootstrap an AngularJS application.In simple words, it defines new Angular app</li>
	<li><strong>ng-controller </strong>: The ngController directive attaches a controller class to the view.It defines which controller controls the section/body/div(view)</li>
	<li><strong>ng-repeat </strong>: Instantiates a template once per item from a collection(Iterator directive) . Similar to forEach in raw javascript</li>
</ul>
ng-app defines a `root-scope` for the whole code in previous example. When each ng-controller is encountered new `scope` is defined there. Angular works with these concepts. `$rootScope` and `$scope` variables are used to denote these scope rules.

<strong>Points to note : </strong>
$rootScope defines root Scope and <strong>every application has a single root scope</strong>.
$scope defines scope for each controller .

Let us move on to the Controller Code now(placed in `angularapp/js/app/app.js`)

{%highlight javascript%}
var employeeApp = angular.module('employeeApp', []); // Defines an angular module

employeeApp.controller('EmployeeController',function($scope,$http,$log){
	//$log is used for console log
	//$http is used to communicate with the server 
	//$scope defines the scope of controller
	$scope.employees=[];
	$http.get("http://localhost:1337/employee/")
		 .success(function(data){
		 	$scope.employees=data;
		 	$log.info($scope.employees);
		 });
});
{%endhighlight%}

We have defined an array employees (see the `$scope.employees` here and in ng-repeat in markup code)in Employee Controller . `$http` is used to communicate with remote HTTP servers via XMLHttpRequest or via JsonP. We have an inbuilt Sails API to list all employees, `localhost:1337/employees`  which returns the data of all employees.We can call it via $http in this angular app. 

Before testing please note that `Angular works only if the environment is correct` . So it is better to put the UI files to an Apache/XAMPP server( Easiest way ;) skipping all bower installations).

Here i've my app at `'localhost/angularapp'`. Lift the Sails app and go to `localhost/angularapp` to test,  you won't be able to see anything initially. Look at the console now . (You may see something like this below).


![SailsAngularTutorialPart3-3]({{ site.url }}/images/posts/3/arc-3.png)

The reason beind this error is that Sails have in built <a target="_blank" href="http://en.wikipedia.org/wiki/Cross-origin_resource_sharing">CORS</a> feature enabled .(A powerful feature and useful one). Here we have to modify CORS file to get the result.

CORS file is located at `sailsapp/config/cors.js`

![SailsAngularTutorialPart3-4]({{ site.url }}/images/posts/3/arc-4.png)

You will see the following line 
{%highlight javascript%}
allRoutes: false,
{%endhighlight%}
Change the above line to
{%highlight javascript%}
allRoutes: true,
{%endhighlight%}

<strong>Note:</strong> This allows API call from any domain . On production you have to set it to custom domains only.

Now lift the sails app and reload Angular UI . You will get the result as shown below.

![SailsAngularTutorialPart3-5]({{ site.url }}/images/posts/3/arc-5.png)

Let us look into the details of what is happened there . An API call from Angualar is done using $http. You would be interested in viewing the responses of that API call. We have logged this and can be viewed in browser console as shown below.

![SailsAngularTutorialPart3-6]({{ site.url }}/images/posts/3/arc-6.png)


The API call returned a JSON array with 2 employees(number depends on the employees you have created).

{%highlight javascript%}
$scope.empList=data;
{%endhighlight%}
This updates the empList which updates the view , even without a refresh. 

Here we have seen how we can use Angular Js with Sails REST apis, we will see the power of Angular in coming tutorials . We can have a more advanced examples with `findEmployeebyEmpnum` API in next tutorials .See you there bye :)
