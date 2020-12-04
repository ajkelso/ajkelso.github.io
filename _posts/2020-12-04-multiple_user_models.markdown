---
layout: post
title:      "Multiple User Models"
date:       2020-12-04 18:37:55 +0000
permalink:  multiple_user_models
---

It seems that most of the time, having only 1 user model gets the job done when building an app with standard authentication.  A Customer signs up, signs in, views Products, and can access information like most other users, and signs out.   

However, I quickly discovered that things get more complicated when dealing with more than 1 user model after I started building my Rails Portfolio Project.

My project is an Audition Tracker App which allows 3 different user models (Actors, Directors, CastingDirectors) to track and make Notes for their Auditions.  I decided to create the 3 separate user models because each user plays such different roles in the Audition process, and  I wanted each user to be able to keep track of which other "users" they had relationships with.  For example, I wanted Actors to be able to view all the Directors that they have auditioned for (ie: @actor.directors).

I quickly found that this was going to make my app MUCH more complex than I had originally thought for many reasons.  But the first problem that needed to be solved was how to sign in each individual user.  If I used the standard `session[:user_id]`, my app wouldn't be able to tell the different between and Actor who's id = 3 and a Director who's id = 3.

The solution I came up with was to prepend each users id with their user model in the session hash.  Instead of `session[:user_id]`, the key became `session[actor_id]` or `director_id` or `casting_director_id` accordingly.  This certainly made things a bit more complicated as many of the standard Rails procedures for granting access to parts of the app for certain users also needed to be modified.  For example, my `current_user` method when from one #find_by method to this:

```def current_user

        if session[:actor_id]
				
            @current_user ||= Actor.find_by(id: session[:actor_id])
						
        elsif session[:director_id]
				
            @current_user ||= Director.find_by(id: session[:director_id])
						
        elsif session[:casting_director_id]
				
            @current_user ||= CastingDirector.find_by(id: session[:casting_director_id])
						
        end
				
    end```
		
This works and allows the app to verify the user type by simply checking to see if their model_id exists within the session hash. For example, a user may not have access to every Casting Director's contact information if `session[actor_id]` is present.

While this solution has certainly created a few bumps in the standard Rails procedure, the refactoring has been manageable (though frustrating at times!), and has allowed my app to function with the extra  features that were originally intended.




