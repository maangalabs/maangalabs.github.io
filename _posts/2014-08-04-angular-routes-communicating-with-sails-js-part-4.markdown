---
layout: post
title: "Angular routes communicating with Sails JS - Part 4"
date: 2014-08-04 21:25:04 +0530
comments: true
categories: sails angularjs webdevelopment nodejs
author: pranavrajs	
featured_image: /images/posts/4/routes-3.png
---

Hi all,

In the last tutorial we have seen how to use Sails REST apis with Angular JS , this time we'll do a more advanced experiment. You might be familiar with fancy urls like `yourdomain.com/profile/username`. What if this feature can be achieved in simple efforts , it would be nice right? So today we will learn about Angular Routing and i'll be using Twiiter Bootstrap to spice up the website :) We'll create two pages one which lists all employees and another on which shows employee profile (url - /profile/employeeid )

<!-- more -->
So lets get started.


We need to create a vanity URL here (without .html or .php etc in the url ). For that we use angular routing technique . In the newer editions of AngularJS , they recommend to use `angular-route.js` file to achieve this .This can be found here . <a href="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.0rc1/angular-route.min.js">Click here for Angular Routes File</a>.

<h4>Basic Configuration</h4>
Create a directory structure as listed below for Angular App 

![SailsAngularTutorialPart4-1]({{ site.url }}/images/posts/4/routes-1.png)

Now we move on to the Markup Section .Here is the first markup needed, the main page used for routing .
`index.html` can be seen as follows 
{% highlight html %}
<!-- index.html -->
<html ng-app="employeeApp">
<!-- ng-app Employee App -->
<head>
	<!-- Add bootstrap to Page -->
	<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
	<!-- Add Open Sans Font -->
	<link href='http://fonts.googleapis.com/css?family=Open+Sans' rel='stylesheet' type='text/css'>
</head>
<body>
	<!-- The views will be loaded here  -->
  	<div ng-view></div>
  	<!-- Add core Angular File -->
	<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.1/angular.min.js"></script>
	<!-- Add Angular route file -->
	<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.0rc1/angular-route.min.js"></script>
	<!-- Add controller file -->
	<script src="js/app/app.js"></script>

</body>

</html>
{% endhighlight %}

Let us move to the first view :) . The main page which lists all employee details and the link to their profiles 

{% highlight html %}
<!-- home.html lists all employees and link to their profile -->
<table class="table table-striped">
	<thead>
		<th>Sl. No</th>
		<th>Employee Name </th>
		<th>Link to profile</th>
	</thead>
	<!-- ng-repeat to iterate over Employee array -->
	<tr ng-repeat="emp in empList">
	<!-- $index+1 gives the value 1,2 etc according to no of iteration -->
	  <td class="active">{ {$index+1} }</td>
	  <td class="success">{ {emp.name} }</td>
	  <!-- Note here that there should not be spaces between angular brackets -->
	  <!-- #profile/profileid is the link to profile -->
	  <td class="info"><a href="#profile/{ {emp.empnum} }">Click here</a></td>
	</tr>

</table>
{% endhighlight %}

Here `$index` gives the iterator offset of the repeated element (0..length-1).

The second view - profile view - which shows the details of a single person. Here we'll use the API which we have generated in the second part of the tutorials . For those who haven't seen the API view it here . <a href="http://maangalabs.com/blog/2014/07/26/generating-rest-api-with-sails-js-part-2/">Click here to view the API -- findEmployeebyEmpnum</a>. We will use the same api here also to search employee by his employee number.

{% highlight html %}
<!-- profile.html - shows employee details  -->
<!-- Show the table if Employee returned by API is defined  -->
<table ng-show="!notFound" class="table table-striped">
	<tr>
	  <td><strong>Name : </strong></td>
	  <!-- Print the name -->
	  <td>{ {employee.name} }</td>
	</tr>
	<tr>
	  <td><strong>Employee Number : </strong></td>
	  <td>{ {employee.empnum} }</td>
	</tr>
	<tr>
	  <td><strong>Email id : </strong></td>
	  <td>{ {employee.email} }</td>
	</tr>
</table>
<!-- Show error if employee is not defined  -->
<div ng-show="notFound"  class = "alert alert-danger col-md-12">
	Error !!! Employee not Found
</div>
{% endhighlight %}

Here `ngShow` directive shows or hides the given HTML element based on the expression provided to the ngShow attribute.The attribute logic is set in the controller. We can now move on to the controller logic. 

Define the `angular app employeeApp` and include ngRoute for our routing purposes
{% highlight javascript %}
var employeeApp = angular.module('employeeApp', ['ngRoute']); // Defines an angular module
	//Include ngRoute for our routing purposes
{% endhighlight %}

Configure the app for routing as follows. 
{% highlight javascript %}
//$routeProvider is injected
employeeApp.config(['$routeProvider',function($routeProvider){
	$routeProvider.when('/',{
							templateUrl:'home.html',
							controller:'EmployeeController'
				    })
				    //.when adds new route definition to $route service
				   .when('/profile/:profileid',{
				   			//Profile View Routing to profile.html and controller - ProfileController
							templateUrl:'profile.html',
							controller:'ProfileController'
					})
					.otherwise({
							//default routing
							redirectTo:'/'
					});
}]);
{% endhighlight %}

`$routeProvider` is used for routing purposes. `when` adds new route definitions.

<strong>Points to note here : </strong>Here profile/:profileid is one of the routes where `profileid` is the parameter passed (In /profile/123 profileid is 123). This parameter can be accessed in ProfileController using `$routeParams` and can be accessed as `$routeParams.profileid` .
We will see this when ProfileController is explained.


We'll move on to the controller logic now.
{% highlight javascript %}
//EmployeeController - lists all employees 

// Injects $scope , $http , $log
employeeApp.controller('EmployeeController',function($scope,$http,$log){
	//$log is used for console log in Angular js
	//$http is used to communicate with the server 
	//$scope defines the scope of controller
	$scope.empList=[];
	$http.get("http://localhost:1337/employee/")
		 .success(function(data){
		 	$scope.empList=data;
		 	//Update empList with data
		 	$log.info($scope.empList);
		 });
});
{% endhighlight %}

This is the same logic as we saw in last tutorial . We'll now move on to the most awesome part of the tutorial Generating the views based on $routeparams.

We can have the code for the ProfileController first.

{% highlight javascript %}

//Profile Controller $scope,$http,$log,$routeParams injected


employeeApp.controller('ProfileController',function($scope,$http,$log,$routeParams){
	//Store empnum in Controller
	$scope.profileid = $routeParams.profileid;
	//Initialist the employee Data
	$scope.employee={};
	//Initialise Error Handler
	$scope.notFound = false;
	//Do an API Call to findEmployeebyEmpnum with $routeParams.profileid
	$http.get("http://localhost:1337/employee/findEmployeebyEmpnum/"+$scope.profileid)
		 .success(function(data){
		 	//On successful API CALL check whether empty data is returned or not
		 	if(data.notFound === true)
		 	{
		 		//If employee not Found set error flag -- ng-show manages the rest 
		 		$scope.notFound = true;
		 		return;
		 	}
		 	//if employee found copy employee Data
		 	$scope.employee=data.userData;
		 	//Log the data
		 	$log.info(data);
		 })
		 .error(function(data){
		 	//Log error Data
		 	$log.info(data);
		 });
});

{% endhighlight %}

We are accessing the routeparameter using $routeParams in the controller . Our API call returns a data as follows .

![SailsAngularTutorialPart4-5]({{ site.url }}/images/posts/2/api-5.png)


We have `notFound set to true` if there are no employees with given employee number , Check this in the success function and set an errorFlag according . Here we'll understand the power of `ng-show` We have previously given attributes for ng-show as `notFound`  flag . We will show the table containing information of employee if `notFound is false` . 
This is achieved in the code as follows

{% highlight html %}
<table ng-show="!notFound" class="table table-striped">

<!-- Refer to the code above profile.html -->
{% endhighlight %}

If `notFound is true`show Error Alert.

{% highlight html %}

<div ng-show="notFound"  class = "alert alert-danger col-md-12">
	Error !!! Employee not Found
</div>
<!-- Refer to the code above profile.html -->
{% endhighlight %}

We can see the end result now. 
Lift the sails app and hit the browser with http://localhost/angularapp/ . You can see that it is redirected to http://localhost/angularapp/#/ . If this is done then the work you have done is correct . You will get a screen like this 

![SailsAngularTutorialPart4-2]({{ site.url }}/images/posts/4/routes-2.png)

You will get your employee list , click on any link and notice the URL structure right now . It will be something like this `http://localhost/angularapp/#/profile/123`.

Here your have created a fancy URL in short span of time . 
The Profile page will be like the one below

![SailsAngularTutorialPart4-3]({{ site.url }}/images/posts/4/routes-3.png)

Now check the error page also . Check with the url `http://localhost/angularapp/#/profile/10000` or any other employee number which is not in the list .

It will now show a screen as seen below 

![SailsAngularTutorialPart4-4]({{ site.url }}/images/posts/4/routes-4.png)

Hooray!!! We have completed the routing part very easily. Next time we'll discuss about the adapters in Sails Js and how you can tune it to use multiple databases - A hybrid database architecture with Sails having both relational databases like MySQL and non-relational databases like MongoDB. Thanks for reading and see you there :) 

<strong>Update : </strong> The file for this tutorial can be seen <a href="https://github.com/pranavrajs/maangalabs-tutorials/tree/master/sails-tutorials/angular-routes-communicating-with-sails-js-part-4" target="_blank">here</a> 
