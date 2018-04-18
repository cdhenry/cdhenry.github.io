---
layout: post
title:      "Rails Project"
date:       2018-04-18 09:41:41 -0400
permalink:  rails_project
---


Rails is an incredible tool for building flexible and clean CRUD applications.  After investing a good deal of time in understanding its functions and grappling with its magical qualities, I was able to create a site with a surprising amount of features.

## The App: 

"Road Trip" is an application that allows users to create, well ...road trips.  Trips can be built by adding previously created destinations or by creating totally new ones.  In addition, destinations have descriptive tags which can also be selected or created.  This can all be done from a single form, or destinations can be created on a form separate from an individual trip.  The app doesn't have all the functionality of one connected to a fancy map api, but it did allow me to play with more complicated join models.  I wanted to challenge myself to juggle a deep set of associations, and I think I did just that:

### Associations:

![](https://image.ibb.co/eskSWn/schema.png)

The three join tables were necessary because I wanted these objects to exist independently, to validate uniqueness, and to associate freely, but that did pose some issues when it came to drawing out forms.

### Implementation:

I began using devise to implement my authentication; however, I found the obscurity of it to be a little too daunting for this project.  I ended up building my own authentication process from scratch, but I hope to go back and do a deep dive into devise at a later point.  It's clearly a powerful gem, but ultimately led me away from more pressing challenges I was interested in tackling.  Also, I made third-party authentication available through github.

After logging in, a user can see a list of other users, road trips, destinations, and tags, as well as various characteristics of each object.  These lists are separate and nicely formatted, and the site is easily navigable through a user-friendly nav-bar.  (This is another challenge I'd set for myself: make the site pretty.  I hadn't played with much css in the past, and I'm proud of how clean it looks).  Lists are also sorted using activerecord queries and group/order methods.

A user can CRUD up these objects with some nested forms.  A single road trip form exists to build everything on one page, and, separately, a destination form exists if a user wants to create spots for others to add to their trips.

### Hazards :

Feature Creep was a definite issue as I started to design the application.  I had fun dreaming up all the things this site could do, but when I got down actually coding it I realized I was way in over my head.  After a couple days just trying to get the skeleton up I ran into an impasse with devise.  A cross-site request forgery issue had me baffled, and I spent another day just learning the ins and outs of devises views, controllers, and helper methods.  It was time well spent, but at that point my application seemed too complicated for me to unravel.  I began again.

Beginning again is a real skill of mine.  The first time through is thrilling: epic goals and the terror of being lost without a compass.  If I do arrive, and that's a big if, I'm tattered and covered in 'character-building experiences'.  It's an utter joy, but the second time around has the reward of actually seeing something built.  It may be scrappy, but it has shape.  

Another issue that came up was with this massive form I was trying to build.  Rails didn't seem to automagically modify associations that had gone through two join tables in one form, so I had to get crafty with how I passed in attributes.  For instance, this is my attribute method for destinations within road_trip.

```
  def destinations_attributes=(destination_attributes)
    destination_attributes.values.each do |destination_attribute|
      stop_order = destination_attribute["stop_order"]
      destination_attribute.delete("stop_order")
      tags = destination_attribute["tags_attributes"].first[1]
      destination_attribute.delete("tags_attributes")

      destination = Destination.find_or_initialize_by(destination_attribute)
      set_tags(tags, destination)

      if destination.save
        self.destination_road_trips.create(destination_id: destination.id, destination_order: stop_order)
      end
    end
  end
```

I found I could create some attr_accessor variables to pass in a few more attributes within the form.  Once I was able to get them into the hash, the above method helped to sort the data into the relevant database modules.

***As I was writing this blog, I had another idea - maybe I could create a form that was for the join table instead of for the RodeTrip model.  Since I was curious, I took another day to build that up; however, it had it's own complications.  For instance, the join model needs both a road_trip_id and a destination_id, but I didn't want the user to always have to create a new destination.  I wanted them to be able to just add destinations that had already been created as well.  I found I could do this, but was having real trouble passing in the stop_order variable mentioned above.  Rather than, spend another morning attempting a solution to that problem, I decided to go with what I had.  It worked, even though it was a bit complicated.

### Accomplishments :

I learned a ton in this project, but there are a couple things of which I'm really proud.  The first is that I think I did a good job of separating the views into relevant partials.  It's really nice when a piece of a view can be implemented in multiple views, and when particularly complex elements are separated into bite size chunks.  The other thing I liked was the section I created for admin controls:

```
rails-road-trip/app/views/destinations/index.html.erb
<% if current_user.admin %>
   <%= render partial: "users/admin/controls", locals: {model: destination} %>
<% end %>

rails-road-trip/app/views/users/admin/_controls.html.erb
<td><span class="badge badge-info"><%= link_to 'Show', model %></span></td>
<td><span class="badge badge-warning"><%= link_to 'Edit', send(edit_path(model), model) %></span></td>
<td><span class="badge badge-danger"><%= link_to 'Destroy', model, method: :delete, data: { confirm: 'Are you sure?' } %></span></td>

rails-road-trip/app/helpers/users_helper.rb
module UsersHelper
  def edit_path(model)
    "edit_" + model.class.name.split(/(?=[A-Z])/).join('_').downcase + "_path"
  end
end
```

This little piece of meta programming allowed each index view to have admin controls by passing in a model.  I noticed my controls all had the same syntax except that the model changed.  I had seen meta programming used in the past to simplify this issue, and I'm happy with how this turned out.

### Goals for the Future :

1. Get in to TDD.  This app was mostly created using EDD, but I found it precarious as the app got more complicated.  I'm setting an aspiration to create tests for my next project.

2. Design Design Design.  I get really excited about possibilities and love coding, so I tend to dive in quickly.  I end up learning a lot from all the trial and error, so maybe that's okay at this stage of the game.  However, in the future I plan to spend a little more time in the design phase drawing out concepts on paper and testing them in my mind before putting fingers to keys.
