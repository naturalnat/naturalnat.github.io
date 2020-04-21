---
layout: post
title:      "Rails Portfolio Project"
date:       2020-04-21 17:54:36 +0000
permalink:  rails_portfolio_project
---

The Ruby on Rails portofolio project was a lot of fun to work on -- it is incredible how 'intuitive' Rails is. Whereas there is a lot of manual routing done through Sinatra controllers, Rails uses RESTful architecture for routing. Rails, comparatively, feels a lot more intuitive. 

The idea behind my project -- "Milkmaid" -- came from reminiscing about my childhood growing up, and the local milkmaid, who would deliver fresh milk locally, in glass jars, which she would then pick up and reuse. Thus, I decided I would make an app that allows you to do the same -- place an order for milk that would be delivered in environmentally friendly packaging. Users are able to place and manage their orders and reviews of items. 

The most trouble I ran into during the project was with ActiveRecord associations -- it created a lot of errors for me when my associations were not exactly correct. As such, I received errors when trying to create or edit an object. Another important thing I learned while making this app was the difference between various find_by methods. For example, when creating the option to edit orders, I originally had '@order = Order.find_by(params[:id])'.  While this was able to locate the correct order, it was ultimately an incorrect order that resulted in being edited. Changing it to find_by_id fixed that error. 

Another fun thing to use were helpers for associations, such as this one in my Orders helper: 
``  def self.getmilk(order_id)
    @order = Order.find(order_id)
    @order.milktype
  end 
``
This finds the order and the milk associated with the order. Such helpers are not limited to order and related contents, and I have used them throughout the app to show features such as usernames. 

Omniauth was another interesting thing to learn - I was always curious about how such authentications work, and now I know! I also played with CSS more for this project than for Sinatra, so I think aesthetically it is also better. While not perfect, I am proud of this assignment. 
