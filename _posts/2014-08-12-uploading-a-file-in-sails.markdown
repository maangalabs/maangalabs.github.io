---
layout: post
title: "Uploading a file in Sails"
date: 2014-08-12 20:24:28 +0530
comments: true
categories: sails webdevelopment nodejs
author: pranavrajs
github_repo_username : maangalabs
github_repo : uploading-file-in-sails
featured_image: /images/posts/7/upl-9.png
---


Hi all

Here is a new tutorial in the Sails and Angular Series. Actually i was thinking about writing a tutorial on loading effects during Sails Angular API calls . This will be handled in next tutorials.To keep you engaged today we will see how an upload function can be created in Sails.
<!-- more -->
<h4>Configure route and layout</h4>

Before we create an upload function , we have to create a form that takes input. Here we can use Sails layouts which is based on ejs templating. 

Create a view `uploadfile.ejs` in views folder( `app/views/` ) as shown below.
 
![UploadingfileinSails-1]({{ site.url }}/images/posts/7/upl-1.png)

Now we can create a form which accept file as input . `enctype="multipart/form-data"` is used to take file,image etc as input .Code is shown below.

{% highlight html %}
<!-- enctype="multipart/form-data" -->
<form id="uploadForm"
      enctype="multipart/form-data"
      action="/file/upload"
      method="post">
    	<input type="file" name="uploadFile" />
    	<input type="submit" value="submit"/>
</form>

{% endhighlight %}
	
Now configure a route to this file . This can be done in `routes.js` file (located in app/config/routes.js).You will see the following code .
{% highlight javascript %}
module.exports.routes = {


  // Make the view located at `views/homepage.ejs` (or `views/homepage.jade`, etc. depending on your
  // default view engine) your home page.
  //
  // (Alternatively, remove this and add an `index.html` file in your `assets` directory)
  '/': {
    view: 'homepage'
  },

  // Custom routes here...


  // If a request to a URL doesn't match any of the custom routes above,
  // it is matched against Sails route blueprints.  See `config/blueprints.js`
  // for configuration options and examples.

};

{% endhighlight %}

You can add custom routes as follows

{% highlight javascript %}
  '/upload-file':{
    view: 'uploadfile'  // view 'uploadfile' in views directory will loaded automatically
  },
{% endhighlight %}


<h4>Configure controller</h4>

Upto here we have created a view for uploading our file to server. Now we move on to controller part where we configure the file upload path . Sails uses `skipper` to upload file in which it uploads the files to `.tmp/uploads`. We will now see how this can be acheived. 

Create a File controller as shown below.

![UploadingfileinSails-2]({{ site.url }}/images/posts/7/upl-2.png)


We will add new function upload in File controller which can be accessed through REST api `/file/upload` . We will block access to `upload API` through GET method . When upload function is complete we will return the file details with status 200.
{% highlight javascript %}
  upload: function  (req, res) {
		if(req.method === 'GET')
			return res.json({'status':'GET not allowed'});						
			//	Call to /upload via GET is error

		var uploadFile = req.file('uploadFile');
		console.log(uploadFile);

	    uploadFile.upload(function onUploadComplete (err, files) {				
	    //	Files will be uploaded to .tmp/uploads
	    																		
	    	if (err) return res.serverError(err);								
	    	//	IF ERROR Return and send 500 error with error
			
	    	console.log(files);
	    	res.json({status:200,file:files});
	    });
	},
{% endhighlight %}

<ul>
	<li><strong>req.method :</strong> Returns the method by which API is called . GET,POST etc.</li>
	<li><strong>req.file : </strong> Returns the name of file being uploaded</li>
	<li><strong>.upload function : </strong> Upload the files to server (.tmp/uploads)</li>
</ul>

Now will lift the app and see whether the code is correct or not.Go to `http://localhost:1337/upload-file`.You will be able to see the following screen.

![UploadingfileinSails-3]({{ site.url }}/images/posts/7/upl-3.png)

Now try adding a file and hit on submit.

![UploadingfileinSails-4]({{ site.url }}/images/posts/7/upl-4.png)

The response screen will be like this.

![UploadingfileinSails-5]({{ site.url }}/images/posts/7/upl-5.png)

Now check whether a file uploaded in the folder or not.Execute the following commands from Sails Directory.
{% highlight text%}
cd .tmp/uploads
ls
{% endhighlight %}

![UploadingfileinSails-6]({{ site.url }}/images/posts/7/upl-6.png)

Yaay!!! It works . 

<h4>Uploading to a custom folder</h4>

Here we could upload the file to .tmp/uploads . So how do we configure it with a custom folder , say 'assets/images'. We can achieve this by adding options to upload function as shown below.


`The tutorial has been updated to work with latest Sails version .`

{% highlight javascript %}

	// Configure with 'dirname' 

    uploadFile.upload({ dirname: '../../assets/images'},function onUploadComplete (err, files) {				
    // Earlier it was ./assets/images .. Changed to ../../assets/images
    //	Files will be uploaded to ./assets/images
    // Access it via localhost:1337/images/file-name


	    	if (err) return res.serverError(err);									
	    	//	IF ERROR Return and send 500 error
			
	    	console.log(files);
	    	res.json({status:200,file:files});
	    });
	
{% endhighlight %}

You can add an option `dirname` (string) to upload to a custom folder .For more see `skipper-docs`.

Check whether this is okay or not.

![UploadingfileinSails-7]({{ site.url }}/images/posts/7/upl-7.png)

The API returns the same content as seen in previous test. I've uploaded an image , so let us check whether it is accessible thorugh browser. 

![UploadingfileinSails-8]({{ site.url }}/images/posts/7/upl-8.png)

Yeah !! Everything is fine . Here we have set a folder only . If we want a custom name for the file , we can generate it by using UUID or date and time and pass it as a parameter as `saveAs` in upload options. 

That's all for today . We'll see the loading effects in Sails Angular API calls in next tutorial . See you there bye. :)

<br/>


