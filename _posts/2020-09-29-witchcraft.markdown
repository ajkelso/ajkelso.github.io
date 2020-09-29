---
layout: post
title:      "Witchcraft"
date:       2020-09-29 20:08:12 +0000
permalink:  witchcraft
---


Several weeks ago, Sinatra was just one of the greatest singers to ever live.  One of my favorites, I might add, and someone who's musical catalog has been unavoidable and a pretty big part of my life.

Now, it's complicated.

I can't think of Sinatra without also thinking about ActiveRecord, Ruby methods, or the word 'ditty'. And I'm almost certain that most people will think that I've lost my mind when I tell them that Sinatra helped me code and build my first website from scratch.  But That's Life.

I began the Mod 2 Sinatra project feeling somewhat overwhelmed by the scope of it all.  For the most part, I understood all the individual concepts behind the requirements of the project: I understood how ActiveRecord helps to persist data in a database, how models were built to relate to eachother, how a controller manages different routes, how HTML & CSS allow us to view the information in a browser, etc, etc.  However, tying everything together after only recently absorbing this tsunami of information was the biggest challenge.  I found my brain wanting to go in 20 differnt directions at the same time: I'd start to build a form, get sidetracked building the POST route for the form, and get sidetracked again building an erb for the POST route for the form, and then try to figure out my stylesheet for the erb before I realized that I never actually finished buidling the erb OR the POST route OR the form itself.  And then the cycle would start all over again.

My own work flow quickly became just as impotant as the code that I was writing.

I eventually got the ball rolling and the basic structure of my project came together.  My site is a Christmas Wish List generator and it allows a User to signup/signin, create a List with many Items, view, edit, or delete their lists, and view everyone's lists.

One of my biggest problems was trying to show my User their List(s) in a specific order.  My Items each have a :ranking attribute which allows a User to rank their Item on a scale of 1-10 by how badly they want the item.  I wanted my users to be able to view each list in descending order by how badly they want the Item.  

In my models, My Item belongs_to a List which belongs_to a User and my User has_many Items through Lists. So-for a User's List to be ordered by ranking, my first thought was to iterate through each User's lists and create new arrays using #sort_by in order or descending ranking: 

`@user = User.find(params[:id])
ranked_lists = @user.lists.each do |list|
  list.sort_by {|item| -item.ranking}
end`

I thought for sure this would work.  And in theory, it does.  However, it is possible for a User to create a List without any Items and it is also possible to create an Item without a :ranking which saves the ranking as 'nil'. And #sort_by will error out when passing through a value of 'nil'.  I didn't want to force a User to rank an Item if thet didn't want to.  Instead, I just wanted those Items to be shown at the bottom of their list.  So...I had to get creative.

I created a few helper methods that not only allowed me to order my lists by ranking, but also to create hashes for each list which contained all the information needed in the erb. So, for my '/lists' route which shows a logged in User everyone's Lists, the solution was this:

1) Gather all the Lists that contain Items: 

`def get_valid_lists(List.all)
 lists.select {|list| !list.items.empty?}
  end`

2)  With the list generated from #get_valid_lists, I iterate through each list and create a hash for each list which contains keys for :list, :user, and :items and which will be useful in my erb.

`def create_list_hash(list)
   hash = {list: list, user: User.find(list.user_id).name, items: sort_by_item_ranking(list)}
    end`

     2a) Within this #create_list_hash helper method, I've created a separate helper method, #sort_item_by_ranking which will sort each list by Item ranking.  This method ends up being the most "messy" due to some Items which have "nil" as a ranking:
		
		`def sort_by_item_ranking(list)
		   
			      # separate unranked Items from ranked Items
            unranked = list.items.select {|item| item[:ranking] == nil}
            ranked = list.items.select {|item| item[:ranking] != nil}
            #order the ranked items in descending order
						sorted = ranked.sort_by {|item| -item[:ranking]}
						#merge the ranked and unranked Item arrays unless there are no unranked Items
            sorted.push(*unranked) unless unranked.empty?
						#return the full sorted array of Items
            sorted
        end`
				

3) And finally, I created a helper method which puts all of the above helpers together:

```def gather_list_hashes(lists)
            get_valid_lists(lists).map do |list|
                create_list_hash(list)
            end
        end```
				
4) Which makes my '/lists' route look like this:

```get '/lists' do
        if logged_in?
            @lists = gather_list_hashes(List.all)    
            erb :'lists/lists'
        else
            redirect '/login'
        end
    end```
	

This code works and gets each individual list to be sorted by ranking.  I haven't done it yet, but the next step for me will be to show each list grouped by their User in the all '/lists' route.  



