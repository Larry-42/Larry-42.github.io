---
layout: post
title:      "CLI Ruby Gem:  Budget Bytes CLI--Refactoring"
date:       2017-12-10 14:30:00 +0000
permalink:  budget_bytes_cli_refactoring
---

Well, I made it through my assessment for my CLI gem, [Budget Bytes CLI](https://larry-42.github.io/budget_bytes_cli_gem).  As it turns out, there were a couple of things that I had to refactor.  If you're looking to do your CLI gem project, these issues may also pop up for you.  

The first issue that I had was that I assigned much of the scraping to the Category class (responsible for keeping track of the recipes within a category), and to the Recipe class (responsible for keeping track of the instructions and ingredients for an individual recipe page).  Both the Recipe and Category classes were responsible for scraping their own data.  By convention scraping is assigned to a scraper class; it is generally considered "cleaner" to separate scraping into its own class.  

So, I moved the scraping functions into my scraper class to separate out the concerns.  This separation of concerns may not be a huge issue for the CLI gem, but I feel like it may become more important in more complex projects, with more "moving parts".

The second issue that I had to refactor away was more of an ugliness about my code.  One thing that I didn't get as comfortable as I should have with was the way that Ruby returns the return value of the last statement in a function.  Whew.  In case this is a bit confusing, consider the following code:

```
def foo(bar)
	baz = bar.each {|b| b + 1}
	baz
end
```

`foo` as written above is a perfectly valid Ruby function, but it's a bit ugly.  And it can be written much better.  If bar is an array, `bar.each {|b| b + 1}` returns an array where all of the elements of bar are increased by 1.  There's no need to assign the results of each to a variable, then return the value.  So, it's possible to refactor `foo`, as seen below:

```
def foo(bar)
	bar.each {|b| b + 1}
end
```

This example may be a bit trivial, but there were a few examples of things like this that I had to refactor away--places where I did not really take advantage of the way that Ruby returns the last expression in a function.  Part of this may be some "baggage" I carry around from self-teaching things like VBA earlier in life.  It's taking a little bit of time to get a feel for of Ruby's idioms--the way that things "should" be written in Ruby to be clean, understandable, and conventional.

Even with both of the corrections noted above, I still feel like there may be some things that could be written better.  There is ALWAYS something left to refactor.  So, after doing the refactoring noted above I updated the version number, pushed to Github, and decided to move on.  Will see what Rack, SQL, and Sinatra are like!
