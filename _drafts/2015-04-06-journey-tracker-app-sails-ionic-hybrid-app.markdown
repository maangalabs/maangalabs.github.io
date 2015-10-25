---
layout: post
title: "Journey Tracker Application"
date: 2015-07-28 18:24:11 +0530
comments: true
categories: sails nodejs ionic mobileapp webdevelopment
author: pranavrajs	
---

Hello everyone,

It has been a long time since i've interacted with you. This time i've something exciting for you. We will create a simple journey tracker application , which is powered by a cloud storage. So you might be wondering what a journey tracker app do, it's very simple. For those who travel a lot, they might have some exciting moments/notes/experience about a place . What you do currently is to note it down in your book , right ? What if , you miss it. We don't want to do that, right ? Let us have an app to note your memories,photos,notes etc. Let us provide a cloud storage.

In a nutshell , we will do the following 

- An mobile app , obiviously a cross platform app. (We use ionic Framework , because it uses Angular ;) It makes my job simple :) )
- A cloud backend powered by Sails.js

How to do this ? 

- Whenever user wants to add data, identify his position , save what ever he added , say a note or a photo
- Save the data in server
- Load Maps and fetch data from server , plot the locations he has visited , in another tab show the details in each place

We want an app runs on all platforms, we can use Ionic Framework for this , and for cloud backend we use Sails server.

***Getting Started with Ionic***

Ionic is a beautiful, open source front-end SDK for developing hybrid mobile apps with HTML5. Ionic is an awesome framework which bundles Cordova with AngularJs.

