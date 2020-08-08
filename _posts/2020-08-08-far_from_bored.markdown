---
layout: post
title:      "Far from Bored"
date:       2020-08-08 14:14:39 +0000
permalink:  far_from_bored
---


For my Flatiron, Mod 1 project-CLI application, I chose to write a program that helps users find an activity to do when they are bored...which I find kind of funny because between school, raising 2 tiny humans, and house projects, I think its pretty unlikely that I will ever need to use my own app.  Anyone with kids during COVID will tell you that they get jealous/borderline angry each time they see a friend post about how bored they are during lockdown or about all the organizing they have accomplished.  So maybe this app was my way of 'putting it out there' that someday, I might have use for such a thing.  Either way, for the right person, this little app could come in handy...or at least pull them away from Netflix for a few minutes.

The app starts by asking if the user is bored, then gives a list of categories to choose from if they would like help finding an activity.  After they choose their category, they might be given a list of subcategories to choose from in order to narrow down their suggestion even further.  The app uses boredapi.com to fetch an activity and return the suggestion to the user who can decide if they want more suggestions or to quit the app.  After they have chosen enough suggestions, they app then lists all their suggestions and exits the program.

From the beginning, I was pretty clear on what I wanted the app to accomplish, and had a general idea on how to achieve that goal, but the devil is in the details, and the 'how' was a bit trickier than I had anticipated.

For one thing, the Bored API does not have an endpoint for the entire database of activities.  Instead, you can either get 1 activity at random or 1 activity based on a parameter (type of activity, price of activity, etc...).  So instead of fetching all the data and creating objects in a Suggestions class which I could then iterate through in order to give my user their suggestion, I needed to find a way to create unique urls/endpoints based on the user’s selections.  

The code that I needed to write in order to translate the user’s choice into a usable URL came fairly easy, but I wasn't sure about the cleanest way to put it in my program.  At first I made separate classes for each of the original parameters that needed additional information: a Type class, a Price class, and a Participants class.  These classes each contained methods which allows the user to choose from another, sub-category list, translate their selection, and create a usable URL for my API.  Some of these methods were so similar, that I eventually created a module that was included with each class.  

However, after looking at all my hard work and getting my app to run, something didn't feel right. I wasn't sure that I was using those classes in best practice.  For one thing, as the program ran, it seemed like I was making 2 objects from different classes that were really the same thing: an object in one of my Category classes (Type, Price, or Participants) and another object in my Suggestions class which is ultimately what gets returned to the user.  The Category class object should really be an attribute of a Suggestions class object and while I thought about making the 2 relate to each other, that didn't feel right or necessary either.  I ultimately came to the conclusion that my Category classes weren't being used to create usable objects as much as they were being used to separate sections of code and make my CLI class seem less cluttered.  They were being used to create a URL, but nothing more. 

I was trying and abstract the methods in my CLI class, but instead, I had created classes whose only purpose was to host specific code that could've lived in my CLI class.  I realized that because I needed my methods to be very specific and tailored to each category in order to create usable URLs, my classes were only making my program unnecessarily complex, but no more abstract.  

So...I deleted my Type, Price, Participants classes & my module, took the code that I had written in those classes, and reorganized it into different methods in my CLI class.  After I got my app working again, this felt much better and less confusing: The CLI class runs the program;  It eventually creates a URL which gets passed to a new API class object to fetch the suggestion data;  The CLI class then uses the new API object to create a new Suggestions class object with its own :type, :price, :participants attributes and this information is what ultimately gets printed for the user!

I know that this is just the tip of the iceberg when it comes to Object Oriented Ruby.  So far, I can tell that it is powerful, flexible, and can be used in many different ways...even if those ways aren't in best practice!  But as I continue on this journey, I look forward to understanding the best ways to use OORuby and while it could get a little confusing at times, I am pretty certain that I will not be bored!

