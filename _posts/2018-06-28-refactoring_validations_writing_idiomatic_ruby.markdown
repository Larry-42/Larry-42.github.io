---
layout: post
title:      "Refactoring validations:  Writing idiomatic ruby"
date:       2018-06-28 10:25:24 +0000
permalink:  refactoring_validations_writing_idiomatic_ruby
---


Whenever you write a web app, you're probably going to need to include validations of some sort.  You need to make sure that you don't create two users with the same username and that passwords are valid, for instance.  Now, when you're writing validations (and when you're writing Ruby in general), you want the code to be readable.  When you're writing Ruby, you're doing so because it's more important for you and/or your team to be able to read your code well than for it to run fast.  You want your code to be *idiomatic*--if you're writing Ruby, you should do things in a way that takes advantages of the way that Ruby is designed and make it readable to someone else who's familiar with the way things are done in Ruby.  The code below, which I will fully admit to writing for my [Sinatra project](https://github.com/Larry-42/cool-places)--see [blog post here](https://larry-42.github.io/cool_places_sinatra_project)-- is an example of code that is absolutely, positively, *not* idiomatic Ruby:

```
def place_is_valid?(parameters)
  place_valid = true
  #Validation:  Make sure that none of the fields are empty
  parameters.each do |k, v|
    if v.empty?
      place_valid = false
    end
  end
    
  #Validation:  Make sure that place is not named "deleted"
  if parameters[:name].downcase == "deleted"
    place_valid = false
  end
    
  #Validation:  Make sure that place does not exist
  if Place.find_by name: parameters[:name], location: parameters[:location]
    place_valid = false
  end
    
  place_valid
end
```
Now, the code I wrote above is perfectly functional; it does everything that it's supposed to do as indicated by the comments.  But it's, for lack of a better word, *ugly*.  It's hard to read.  The code explicitly returns a boolean flag, `place_valid` that gets set to false if one of the validations fails.  When I did my Sinatra assessment, I was asked to refactor this code to something nicer.   After the assessment and a little bit of post-assessement analysis and bugfixing, I ended up with the following code:

```
def place_is_valid?(parameters)
  !(!parameters.select {|k, v| v.empty?}.empty? \
	|| parameters[:name].downcase == "deleted" \
	|| Place.find_by(name: parameters[:name], location: parameters[:location])
end
```
Now, that's a lot more concise, is easier for a Rubyist to read, and has the exact same functionality as the monstrosity I wrote earlier!  How did I get there from the first function?  By applying a few basic Ruby concepts--truthiness, using select on a hash (Hash#select), the || (or) operator, and using a '\' to split up long lines of code.  I'll review each of these as I go through the refactoring.
## Truthiness
As described [here](https://gist.github.com/jfarmer/2647362), Ruby can use non-boolean values in boolean contexts (if statements, &&, ||, and so on).  In Ruby, `nil` acts like `false` (they're both "falsey") in boolean contexts.  Everything else is "truthy" (acts like `true` in a boolean context.)  Yes, even 0 is truthy in Ruby!  To see this, let's look at the following code:
```
[nil, false, 0, 42, "stuff", "This statement is a lie."].each do |a|
  if a
	  puts "truthy"
  else
	  puts "falsey"
  end
end
```
This code iterates over the array given, and spits out:
```
falsey
falsey
truthy
truthy
truthy
truthy
```
So, anything other than `false` or `nil` that any operation returns is going to be truthy.  We'll see how that can help us refactor in just a little bit.
## Using Hash #select
The first refactoring we can do is to use [Hash#select](https://docs.ruby-lang.org/en/2.0.0/Hash.html#method-i-select) instead of [Hash#each](https://docs.ruby-lang.org/en/2.0.0/Hash.html#method-i-each) to see if there are any empty values in the parameters we run through #place_is_valid?  Hash#select is a bit more elegant than Hash#each and, more importantly, will lead us to one line that returns a boolean.  Hash#select will select any elements in a hash that meet a given criterion.  So if we enter `parameters.select {|k,v| v.empty?}` we'll get a hash back with any empty elements, or an empty hash if no elements are empty.  In any case, the value we get will be truthy.  To get to a boolean, we need to change to `parameters.select{|k,v| v.empty?}.empty?`  This will be true only if there are no empty elements in `parameters`.  So, we can enter `!parameters.select{|k,v| v.empty?}.empty?` to return `true` if there are non-empty elements in `parameters`.  This lets us change up the monstrosity I wrote earlier to this:

```
def place_is_valid?(parameters)
  place_valid = true
  #Validation:  Make sure that none of the fields are empty
  if !parameters.select{|k,v,| v.empty?}.empty?
	  place_valid = false
  end
    
  #Validation:  Make sure that place is not named "deleted"
  if parameters[:name].downcase == "deleted"
    place_valid = false
  end
    
  #Validation:  Make sure that place does not exist
  if Place.find_by name: parameters[:name], location: parameters[:location]
    place_valid = false
  end
    
  place_valid
end
```
This lets us get rid of a few lines but, more importantly, leaves us with three truthy/falsey values that we can string together using the or operator.
## The || (or) operator
If you've made it this far in this blog post, you're almost certainly familiar with using the or operator, ||, on boolean values.  But the || operator is much more powerful than that.  You can use it on anything truthy or falsey, *i.e.* any object you darn well please.  The || operator will return the first truthy value it sees.  For instance, `"cat" || "dog"` would return `"cat"` since `"cat"` is truthy and it's the first truthy value the || operator sees.  Similarly, `nil || "dog"` would return `"dog"`, since nil is falsey.  On the other hand, if || doesn't see a truthy value it will return the last value seen; that is to say, `false || nil` would return `nil`.  So what does that have to do with the example I'm running through?  Well, if you look carefully you'll see that I've got three `if` statements with truthy/falsey values that I'm using to set a flag.  It's very possible, and a lot cleaner, to simply string these statements with the || operator like so:
```
def place_is_valid?(parameters)
  !parameters.select {|k, v| v.empty?}.empty? || parameters[:name].downcase == "deleted" || Place.find_by(name: parameters[:name], location: parameters[:location]
end
```
There's just one thing wrong here.  That statement will be truthy (say that the place is valid) if one of the flags is true.  We want the opposite.  All we have to do is wrap the statement in parentheses and apply a not operator (!) as seen below:
```
def place_is_valid?(parameters)
  !(!parameters.select {|k, v| v.empty?}.empty? || parameters[:name].downcase == "deleted" || Place.find_by(name: parameters[:name], location: parameters[:location])
end
```
## Splitting lines of code with a backslash
So, we've gotten the monstrosity we saw earlier down to one line of code.  There's just one problem left:  it's still hard to read.  If you don't have word wrap, some of the line will be cutoff in your text editor.  If you do have word wrap, it may wrap in a way that makes it hard to read.  In times like these, you want to split up a single line of code.  You can do this by simply entering a backslash, '\', in your code before hitting enter.  If we split the line just before each || operator, we get the fully refactored code we saw earlier:
```
def place_is_valid?(parameters)
  !(!parameters.select {|k, v| v.empty?}.empty? \
	|| parameters[:name].downcase == "deleted" \
	|| Place.find_by(name: parameters[:name], location: parameters[:location])
end
```
Now, doesn't this look more readable?
## Some final thoughts
Refactoring code can be hard.  Please don't think that I thought through everything here in a few seconds, did things in the order mentioned, worked without Google, or even that I did everything in one sitting.  But if you take advantage of the way that Ruby does things, you can make your code much more readable for your team--and for yourself long after you've walked away from a piece of code.
