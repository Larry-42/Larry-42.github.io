---
layout: post
title:      "Cool Places Sinatra Project"
date:       2018-05-23 16:30:00 +0000
permalink:  cool_places_sinatra_project
---


Well, I've gotten back up to speed, and come through the second project on Learn.co.  This time, I created a project in Sinatra.  After running through several ideas (and running them past the Flatiron staff), I came up with the idea of doing a (very) poor man's [Atlas Obscura](http://atlasobscura.com).  Essentially, the site lets users share places, leave comments on their places (and other users' places!), and edit their own content.

There are projects out there like [Corneal](https://github.com/thebrianemory/corneal) and [Hazel](http://https://github.com/c7/hazel), which create a Sinatra project skeleton.  While there's something to be said for using a generator like that, I started essentially from "scratch", copying relevant gems into my Gemfile from example Sinatra projects, and creating the appropriate directories and files needed (even if some were copied and pasted)--after all, this is supposed to be a learning experience more than anything else.  

With that out of the way, I figured out what my Models would look like and how they'd interact.  I decided I'd have a User model which could own Comments and Places, and that the Comments model would belong to a Place.  After proving out that they worked, I started on the front end.  Important note:  While I may have some regrets down the road about this project since nothing is perfect, I am almost positive I will not regret going from the back end to the front end.  It just seems so logical!

Moving on to the Views and Controllers, I developed the functionality of the app.  One of the more 'interesting' parts was trying to figure out what needed validation.  After I had the functionality down, I went back, reviewed Bootstrap on W3Schools, and added a Navbar.  I also went through and tried to make the site slightly less hideous and more Bootstrappy than it had been.  After adding error and success messages using rack-flash and fixing a few things,  I called it a day on this project.

Could it have more features?  Absolutely.  Any project you develop could *always* use a feature or eight.  But, at the end of the day I had to remind myself--this is not my magnum opus.  There's a reason why Rails is used much more often than Sinatra for non-trivial sites.  And, yes, I know the design is not super--I wanted this project to be more about the back end than the front.  Overall, it feels satisfying to have developed my first almost-from-scratch web app, and hopefully the lessons I've learned will serve me well going forward.
