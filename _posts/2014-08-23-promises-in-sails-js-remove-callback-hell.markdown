---
layout: post
title: "Promises in Sails JS - Remove Callback Hell"
date: 2014-08-23 12:29:44 +0530
comments: true
categories: sails webdevelopment nodejs
author: pranavrajs
featured_image: /images/posts/8/pyr-3.png
---

Hi all,

It has been a week since the last post , i was busy in my academics . Here is our new tutorial on Sails JS . This time its a very useful topic. We'll deal with removing `Callback Hell` (also called `Pyramid of Doom`) in Javascript. 
<!-- more -->
<h4>What's actually Pyramid of Doom in JS ?</h4>
Look at the picture below . This is a popular example from MDN . 


![PyramidofDoom-1]({{ site.url }}/images/posts/8/pyr-1.png)

Here we can see that we are doing the operations on success of related functions which makes code weird. How can remove this problem ?. Here we have a solution . We can use promises by which can remove Callback Hell . 

<h4>Current Problem</h4>

Consider an application where we have 
<ul>
	<li>Student Model with Name , Classroom id , student id</li>
	<li>Classroom Model with Name , Classroom id</li>
</ul>

Here if 
we want to 

`retrieve the list of all students who is in the same class of a given student and the api should return classroom details also`

<h5>Callback / Normal Approach</h5>

<ul>
	<li>Get the details of given student </li>
	<li>On success of the above query , get the details of classroom </li>
	<li>On success of the above query , get the details of all students who are in that classroom</li>
</ul>

This can be achieved by following code segment.

{% highlight javascript %}

	Student.findOne(givenuserid)
		   .exec(function(err,data){
		   		//Code cannot be reused ... Use it for understanding only 
		   		Classroom.findOne(extracted data)
		   				 .exec(function(err,data){

		   				 		Student.find(another data)
		   				 			   .exec(function(err,data){
		   				 			   		// Do something here
		   				 			   	});
		   				 });
		   });
		   // Code only for understanding 
{% endhighlight%}

We can see another approach here . 

<h4>Promises approach</h4>

We'll be using promises which is available in Sails by default.
Promises use the Q library, so anything you do after the first then call (or spread, or fail), will be a complete `Q promise object`. Remember, you must end the query somehow (by calling then or one of the other functions) in order to complete the database request.

{% highlight javascript %}

findallStudents:function (req,res) {
		var id = req.param('id');
		Student.findOne({stdid:id})
				.then(function(stdData){
						//If no student found
						if(stdData===undefined)
								return res.json({notFound:true});
						// Store Class Data	
						var classData = Classroom.findOne({classid:stdData.classroom})
												 .then(function(classData){

												 		var new_data = classData;
												 				delete new_data.createdAt;
												 				delete new_data.updatedAt;
												 		return new_data;

												 });
						var std_data = Student.find({classroom:stdData.classroom})
											  .then(function(allData){
														var new_data = allData;
												 				delete new_data.createdAt;
												 				delete new_data.updatedAt;
												 		return new_data;
											  });
						return [classData,std_data];					  	
				})
				.spread(function(classData,stdData){

					var newJson = {};
						newJson.classname = classData.name;
						newJson.students = stdData;
					return res.json({notFound:false, data:newJson});
				})
				.fail(function(err){
					console.log(err);
					res.json({notFound:true,error:err});
				});

	}
{% endhighlight %}

Here we are not waiting for Classroom data to be fetched , both student details and classroom details are fetched at the same time .

<h4>Working Demo</h4>

![PyramidofDoom-2]({{ site.url }}/images/posts/8/pyr-2.png)

Yaay!!! It works ... We have created an API with promises here . We'll see advanced experiments with Sails in coming tutorials.. See you there bye



