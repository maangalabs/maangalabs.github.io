---
layout: post
title: "Ionic at scale"
date: 2015-08-26 01:07:25 +05:30
comments: true
categories: ionic mobileapp hybrid
author: pranavrajs
featured_image: /images/posts/12/ionic-1.png
---

Hello everyone,

It has been a long time i've interacted with you. This article is not actually a tutorial, but it will be helpful when you use IonicFramework at scale. I've been working with IonicFramework for almost 8 months. I've used Ionic in couple of exciting projects. It works well with small and large projects. 

<!-- more -->
<h4>Project Structure</h4>

The default Ionic project structure is as follows

{%highlight javascript %}
-- www
  -- lib
  -- css
  -- img
  -- js
    -- app.js
    -- controller.js
    -- directive.js
  -- templates
  -- index.html
{%endhighlight%}

I had around 150 views in a single application which made me rethink about the project structure. I started using the similar structure in ngCordova demo application with some changes in it. 

{%highlight javascript %}
-- www
  -- lib
  -- modules
    -- module1        // Angular modules which are injected in app.js
      -- views
        -- view1.html
        -- view2.html
      -- module1.ctrl.js
      -- module1.dir.js
  -- shared           // Shared folder to include common js, css, templates
    -- img
    -- css
    -- views
      -- sidemenu.html
    -- js
      -- app.js
  -- index.html
{%endhighlight%}

This type of project structure helped me to create and update modules easily. Here i've a couple of views and related functions in a single module. I used a shared folder to keep common js/css/templates files.


<h4>App Performance</h4>

***CrossWalk***

When you add more and more modules with heavy HTTP calls , then the normal Ionic build might not work properly in low end phones. Also your Ionic application behaves differently in differently devices according to their browser capabilities. Ionic supports `CrossWalk` project. So we can use an alternate browser for Android. You can see more details in Ionic Documentation page <a href="http://ionicframework.com/docs/cli/browsers.html">Click here</a>

***Grunt***

Grunt is a task runner which can automate you job easily. We need to go through a minification process to reduce the app size and improve performance.

Commonly used grunt plugins are `grunt-contrib-concat`,`grunt-contrib-uglify`,`grunt-jsbeautifier`,`grunt-contrib-copy`,`grunt-cssmin`,`grunt-watch`.

***Images***

You can do a lossless compression of images online to reduce the size of images used. Cache images if necessary using `ImageCacheFactory` <a href="https://github.com/andrewmcgivery/ionic-ion-imageCacheFactory">Link to repo</a>.

Using ImageCacheFactory is simple

{%highlight javascript %}
angular.module('myApp', ['ionic','ionic.ion.imageCacheFactory'])

$ImageCacheFactory.Cache(imgLinkArr)
    .then(function(){
        console.log("Completed loading Image");
    },function(failed){
        console.log("Image loading failed: "+failed);
    });
{%endhighlight%}

<h4>i18n - Translation</h4>

I've used angular translate project where i've loaded 2 json dynamically when app is initiated. `https://angular-translate.github.io`

{%highlight javascript %}
angular.module('translateApp', ['pascalprecht.translate'])

.config(function ($translateProvider) {
  $translateProvider.translations('en', {
    TITLE: 'Hello'
  });
  $translateProvider.translations('de', {
    TITLE: 'Hallo'
  });
  $translateProvider.preferredLanguage('en');
});
{%endhighlight%}

$translateProvider is handy tool when you are building an application with global reach.


<h4>Directives, directives every where</h4>

When you see, you are using repeated code in multiple controllers , then you should think of directives.

<h4>LocalStorage</h4>

Browsers have the capability for storing data in a (key, value) pair called localStorage. Instead of using `window.localStorage`, we can use the Angular Factory for localStorage from Ionic website.

{%highlight javascript %}

app.factory('$localstorage', ['$window', function($window) {
  return {
    set: function(key, value) {
      $window.localStorage[key] = value;
    },
    get: function(key, defaultValue) {
      return $window.localStorage[key] || defaultValue;
    },
    setObject: function(key, value) {
      $window.localStorage[key] = JSON.stringify(value);
    },
    getObject: function(key) {
      return JSON.parse($window.localStorage[key] || '{}');
    }
  }
}]);


{%endhighlight%}

Point to note here is , you are storing the objects as strings . On retreiving , you are parsing the strings back to Objects.

<h4>Plugins</h4>

ngCordova has the wrapper for almost every cordova plugins. `http://ngcordova.com/docs/plugins/`


<h4>Debugging</h4>

You can debug your hybrid application using Chrome remote debugging. To debug, connect your device via adb and run the app. Go to `chrome://inspect/#devices` . You'll find you application with an inspect button.


These are some of the points which i came across when i write this article. I'll update the article with more tools and services that might be useful when you are working on larger projects. Bye

