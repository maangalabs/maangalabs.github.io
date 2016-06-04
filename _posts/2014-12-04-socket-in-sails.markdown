---
layout: post
title: "Socket in Sails with Angular - A demo chat application with Sails and Angular"
date: 2014-12-04 18:24:11 +0530
comments: true
categories: sails nodejs webdevelopment
author: pranavrajs	
github_repo: socket-in-sails
github_repo_username: maangalabs
featured_image : /images/posts/10/sock-4.png
---

Hi everyone,

You might expecting a tutorial on JSON webtokens with Waterlock as i promised you in the last tutorial , but this is not that one. The reason is that when i searched , experimented more on Waterlock , i found an issue with invalidating JSON web-token , i've reported the issue in their Github Page. Hope he'll rectify it. In the meantime we can go for an awesome tutorial on Socket in Sails.js .I'll use AngularJS here along with Sails. We are going to create a chat application (not a real one :D , a demo piece ;) ) with Sails Sockets and Angular.

<!-- more -->
![SailsSocket-1](/images/posts/10/sock-4.png)


<a href="http://sails-socket-maangalabs.herokuapp.com/" target="_blank" class="btn btn-primary" style="padding:10px;"> See the Demo</a>

<a href="http://sails-socket-maangalabs.herokuapp.com/" target="_blank" class="btn btn-primary" style="padding:10px;"> View Source Code</a>


***Introduction to WebSockets***


WebSocket is a protocol providing full-duplex communications channels over a single TCP connection.
There are two main continents in the world of WebSockets

- the client (e.g. browser)
- the server (e.g. your routes, controllers, and so forth).

Socket communication comes in handy when we rely heavily on real-time communication between peers/ client-server etc.With the advent of Node.js , real-time communication has become easier than before. You can use sockets to have real-time communication. You Socket.io with Node to make the task easier. Here we are using Sails MVC on Node.js to create our Socket app .

Sails comes with in-built sockets. Sails consist of several functions that can make your task easier . Please have a look at Sails app with its initial configuration .


![SailsSocket-1](/images/posts/10/sock-1.png)



***Sails Configuration***

Create a sails application 

{% highlight text %}
sails new chat-app
{% endhighlight %}

![SailsSocket-1](/images/posts/10/sock-3.png)

Sails provides <strong>EJS templating</strong>  by default. I like to have raw HTML than EJS/JADE . There will be many people like me right ? I hope so :D . So i'll remove the default templating and will be creating a fresh Bootstwatch + Angular UI. 

How can i use HTML files instead of JADE/EJS ? This was a question many people asked me when i started writing about Sails.js


Here is a simple solution. In your Sails App , go to `config/routes.js`. You will see following code there

{% highlight javascript %}

module.exports.routes = {

  /***************************************************************************
  *                                                                          *
  * Make the view located at `views/homepage.ejs` (or `views/homepage.jade`, *
  * etc. depending on your default view engine) your home page.              *
  *                                                                          *
  * (Alternatively, remove this and add an `index.html` file in your         *
  * `assets` directory)                                                      *
  *                                                                          *
  ***************************************************************************/

  '/': {
    view: 'homepage'
  }

  /***************************************************************************
  *                                                                          *
  * Custom routes here...                                                    *
  *                                                                          *
  *  If a request to a URL doesn't match any of the custom routes above, it  *
  * is matched against Sails route blueprints. See `config/blueprints.js`    *
  * for configuration options and examples.                                  *
  *                                                                          *
  ***************************************************************************/

};

{% endhighlight %}



`Remove the route to '/' as shown below . Keep it blank `

New `routes.js` will look like 

{% highlight javascript %}

module.exports.routes = {

	//Remove '/' :)

};

{% endhighlight %}

Okay !!! now it's done you can use your HTML files in Sails app . Put your index.html in `assets` folder . Sails will now load views from here :) 

You can now delete your views folder in your Sails App .The directory structure at this point will as shown below .

{% highlight text %}

-- chatApp/
	-- api/
		-- models/
		-- controllers/
		-- policies/
		-- responses/
		-- services/
	-- assets/
		-- images/
		-- js/                -------- This is the place for Angular Code
			-- dependancies/  -------- Contains Sails Socket Files
		-- styles/            -------- Contains Bootswatch Files 
		-- index.html         -------- This is your index file for the app
	-- config/
	-- tasks/
	-- node_modules/
	-- Gruntfile.js
	-- app.js
	-- package.json

{% endhighlight %}


***Sails Controllers***

Now we are set with initial configuration, so let us move on to the next part . Configuring controllers and models

Define a chat controller and a chat Model

{% highlight text %}
sails generate api chat
{% endhighlight %}

![SailsSocket-1](/images/posts/10/sock-2.png)

You have Chat model and Chat Controller with you now. 

***Create Chat Model***

Chat model consists of 2 attributes :

- user - defines which user sends the message 
- message - defines the message send

A `user` attribute can be autogenerated or a static one for one socket / user input name . A `message` is the message obtained through sockets

The file `Chat.js` in `api/models/` can be modified with above attributes

{% highlight javascript %}

/**
* Chat.js
*
* @description :: TODO: You might write a short summary of how this model works and what it represents here.
* @docs        :: http://sailsjs.org/#!documentation/models
*/

module.exports = {

	attributes: {
		// Both fields are set to required
		user:{
			type:'string',
			required:true
		},
  		message:{
  			type:'string',
  			required:true
  		}
  		
	}
};


{% endhighlight %}



***Write the controller logic***

Here is place where we have our socket configuration and default controller actions. 

Chat controller has to add new messages and to send message socket clients when a new message is arrived.

I'm adding a new function here `addConv`. The file `ChatController.js` in `api/controllers` now looks like the one below 

{% highlight javascript %}

/**
 * ChatController
 *
 * @description :: Server-side logic for managing chats
 * @help        :: See http://links.sailsjs.org/docs/controllers
 */

module.exports = {

	addConv:function (req,res) {

	}	
};



{% endhighlight %}


The `addConv` checks whether the request is from a socket or not . This can be done by checking `req.isSocket` . If it is a `POST` request , it means a new message should be added . The message as well as username is obtained via `req.param.all()` function. 

So here is our logic 

{% highlight javascript %}

addConv: function(req, res) {

    var data_from_client = req.params.all();

    if (req.isSocket && req.method === 'POST') {

        // This is the message from connected client
        // So add new conversation

    } else if (req.isSocket) {
        // subscribe client to model changes 
    }
}


{% endhighlight %}


When a new Chat Model Instance is created , we will push the corresponding data to connected clients. So here we have to make the clients to watch the model `Chat` and get message when new Chat is created


- <strong>.watch(request) : </strong> This subscribes a client to publishCreate events for the model. Any connections that are "watching" the model class will be automatically subscribed to new model instances that are created using the blueprint create method.
- <strong>.publishCreate(data,[request]) : </strong>PublishCreate doesn't actually create anything. It simply publishes information about the creation of a model instance via websockets. PublishCreate is called automatically by the blueprint create action.

Modify the code with corresponding functions 

{% highlight javascript %}
/**
 * ChatController
 *
 * @description :: Server-side logic for managing chats
 * @help        :: See http://links.sailsjs.org/docs/controllers
 */

module.exports = {

    addConv: function(req, res) {

        var data_from_client = req.params.all();

        if (req.isSocket && req.method === 'POST') {

            // This is the message from connected client
            // So add new conversation
            Chat.create(data_from_client)
                .exec(function(error, data_from_client) {
                    console.log(data_from_client);
                    Chat.publishCreate({ 
                    	id: data_from_client.id, 
                    	message: data_from_client.message,
                    	user: data_from_client.user
                    });
                });
        } else if (req.isSocket) {
            // subscribe client to model changes 
            Chat.watch(req.socket);
            console.log('User subscribed to ' + req.socket.id);
        }
    }
};


{% endhighlight %}

Initially client connects with a GET request to `/chat/addConv/` , then it will be added to subscribe list of Model `Chat` .When new additions are done in Chat model . It will be pushed to all clients who watch the changes.

So by this our server side logic is complete . Pretty simple right ? :) 

***Create Angular UI***

Now we will create an Angular frontend that can communicate with the server . So create an `index.html` file in the folder `/assets/` . 

I'm using the following frameworks

- <strong> Bootswatch - Paper Theme  : </strong> This can be used to create a Material Design like interface which is actually cool :)
- <strong>Angular JS :</strong> Make our job easier :) I don't think i should talk more about this 



Our basic `index.html` will look like

{% highlight html %}

<!DOCTYPE html>
<html >
<head>
	<title>Sails Socket Demo - Maangalabs</title>
	<!-- BootSwatch Paper Theme for Bootstrap -->
	<link rel="stylesheet" type="text/css" href="/styles/bootstrap.min.css">
	<!-- Open Sans Font -->
	<link href='https://fonts.googleapis.com/css?family=Open+Sans:300,600' rel='stylesheet' type='text/css'>
</head>
<body ng-app="socketApp" ng-controller="ChatController">
	<!-- Sails Socket Dependancy -->
	<script type="text/javascript" src="/js/dependencies/sails.io.js"></script>
	<!-- Angular Js  -->
	<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.27/angular.min.js"></script>
</body>
</html>

{% endhighlight %}

Create an angular app add Chat Controller to it :)\

{% highlight javascript %}

var socketApp = angular.module('socketApp',[]);

socketApp.controller('ChatController',['$http','$log','$scope',function($http,$log,$scope){
	// $http , $scope , $log injections
}]);

{% endhighlight %}

Socket connection can be initiated with `io.socket.get` call to `/chat/addConv`.

{% highlight javascript %}
io.socket.get('/chat/addconv');
{% endhighlight %}

Now retrieve the chat history upto now. 

{% highlight javascript%}

$http.get($scope.baseUrl+'/chat')
	.success(function(success_data){

	$scope.chatList = success_data;
	$log.info(success_data);
});

{% endhighlight %}

`/chat` is in-built API with Sails . This may go down when you go for production , you will have to create your own API to retrieve the chat till now. 


<strong>NOTE : </strong> The default number of records to show in the response from a "find" action is limited to 30 in Sails. This can be modified in `config/blueprints.js` . 

`Uncomment` / `Add` the following line


{% highlight javascript%}
defaultLimit: 30000,
{% endhighlight %}


Now let us try to show the chat Messages using Angular JS
{% highlight html %}
<div class="col-md-12" style="padding:100px">
	<table  class="table">
		<tr class="chat_message" ng-repeat="chat in chatList | orderBy:predicate:reverse | limitTo: 15">
			<td class="col-md-12 td_class"><strong>{{ chat.user }} : </strong> {{ chat.message }}</td>
		</tr>
	</table>
</div>
{% endhighlight %}

Here i've used some features of Angular JS

`ng-repeat` contains
- chat iterator
- orderBy
- limitTo

orderBy - filter in module ng
Orders a specified array by the expression predicate. It is ordered alphabetically for strings and numerically for numbers. Note: if you notice numbers are not being sorted correctly, make sure they are actually being saved as numbers and not strings.

`orderBy_expression | orderBy : predicate : reverse`

I've set `$scope.predicate` to `-id` and `$scope.reverse` to false

{% highlight javascript%}

$scope.predicate = '-id';
$scope.reverse = false;

{% endhighlight %}


Now let us try to get socket messages.

The publishCreate socket message to subscribers will include the following properties:

- id - the id attribute of the new model instance
- verb - "created" (a string)
- data - an object-- the attributes and values of the new model instance

<strong>io.socket.on() : </strong> Starts listening for server-sent events from Sails with the specified eventIdentity. Will trigger the provided callback function when a matching event is received.

{% highlight javascript%}

io.socket.on(eventIdentity, function (msg) {
  // ...
});

{% endhighlight %}

Check whether the `verb` is `created`. If so add the data to current ChatList in AngularJS.

{% highlight javascript%}

io.socket.on('chat',function(obj){
	//Check whether the verb is created or not
	if(obj.verb === 'created'){
		$log.info(obj)
		$scope.chatList.push(obj.data);
		// Add the data to current chatList
		// Call $scope.$digest to make the changes in UI
		$scope.$digest();
	}
});
{% endhighlight %}


The `$scope.$digest()` function iterates through all the watches in the $scope object, and its child $scope objects (if it has any). When $digest() iterates over the watches, it calls the value function for each watch. If the value returned by the value function is different than the value it returned the last time it was called, the listener function for that watch is called.

The $digest() function is called whenever AngularJS thinks it is necessary. For instance, after a button click handler has been executed, or after an AJAX call returns (after the done() / fail() callback function has been executed).


Alternatively you can use `$scope.$apply()`

Now we add some input fields to the page to take input from user.

We can take username and message from user. Edit the HTML code as follows
{% highlight html %}
<div class="navbar navbar-inverse navbar-fixed-bottom">
    <div class="col-lg-12">
        <form class="form_chat">
            <div class="col-lg-4 col-md-3">
                <!-- Username -->
                <input type="text" ng-model="chatUser" class="form-control" placeholder="TypeYourNameHere">
            </div>
            <div class="col-lg-6 col-md-5">
                <!-- Chat Message -->
                <input type="text" ng-model="chatMessage" class="form-control" placeholder="TypeYourMessageHere">
            </div>
            <button class="btn btn-default col-lg-2 col-md-2" ng-click="sendMsg()">Send</button>
        </form>
    </div>
</div>
{% endhighlight %}


I use two models here `chatUser` and `chatMessage`. On send call `sendMsg()` function which is described as 
{% highlight javascript%}

$scope.sendMsg = function(){
	$log.info($scope.chatMessage);
	io.socket.post('/chat/addconv/',{user:$scope.chatUser,message: $scope.chatMessage});
	$scope.chatMessage = "";
};

{% endhighlight %}

`io.socket.post` does the same function as POST , but through sockets :) 


So the Angular code will now look like 
{% highlight javascript%}
var socketApp = angular.module('socketApp', []);

socketApp.controller('ChatController', ['$http', '$log', '$scope', function($http, $log, $scope) {


    $scope.predicate = '-id';
    $scope.reverse = false;
    $scope.baseUrl = 'http://sails-socket-maangalabs.herokuapp.com';
    $scope.chatList = [];
    $scope.getAllchat = function() {

        io.socket.get('/chat/addconv');

        $http.get($scope.baseUrl + '/chat')
            .success(function(success_data) {

                $scope.chatList = success_data;
                $log.info(success_data);
            });
    };

    $scope.getAllchat();
    $scope.chatUser = "nikkyBot"
    $scope.chatMessage = "";

    io.socket.on('chat', function(obj) {

        if (obj.verb === 'created') {
            $log.info(obj)
            $scope.chatList.push(obj.data);
            $scope.$digest();
        }

    });

    $scope.sendMsg = function() {
        $log.info($scope.chatMessage);
        io.socket.post('/chat/addconv/', { user: $scope.chatUser, message: $scope.chatMessage });
        $scope.chatMessage = "";
    };
}]);
{% endhighlight %}


I've set the chatUser to `nikkyBot` which can be edited by user. 

Now let us add some css customisations 

{% highlight html%}
body {
    background: #ededed;
    font - family: 'Open Sans',
    sans - serif;

}
.navbar {
    border - radius: 0 px;
}
.form_chat {
    padding: 10 px;
}
.form-control {
    width: inherit;
}
.chat_message {
    padding: 10 px;
    color: #000;
        font-size: 15px;
        background: # fff;
    font - family: 'Open Sans',
    sans - serif;
}
.td_class {
    word -
        break: break -all;
    padding: 34 px;
    padding - bottom: 0 px;
    padding - top: 20 px;
    border: 0;
}
.navbar-brand {
    font - size: 14 px;
    font - weight: 600;
    text - decoration: none;
}
.user_name {
    padding - bottom: 0;
    color: #fff;
    font - size: 15 px;
}
.col - lg - 4, .col - lg - 6 {
    padding - right: 3 px;
    padding - left: 3 px;
}


{% endhighlight %}


All set , ready to rock !!! Lift your app . 

<a href="http://sails-socket-maangalabs.herokuapp.com/" target="_blank" class="btn btn-primary" style="padding:10px;"> SEE THE DEMO</a>


Yeah!!!! It works :)

Hope you have enjoyed the tutorial . Ping me if you need any king of assistance . So See you till the next awesome tutorial :) 
Bye  


