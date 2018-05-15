---
layout: post
title:      "Sinatra Project"
date:       2018-03-25 15:27:51 -0400
permalink:  sinatra_portfolio_project
---


While this project was an excellent opportunity to solidify an understanding of html forms, session authentications, and RESTful routes, the most fulfilling puzzles to solve were those that dealt with activerecord.  ActiveRecord associations are like the infrastructure that makes an emerging world churn.  Constructing them calls into view the space between objects and highlights the importance of the tendrils that connect them.

This is kind of an ornate way to talk about belongs_to and has_many associations in blocks of code, but encountering the question of ownership and creating hierarchical structures, no matter how virtual, always begs some careful consideration before locking-in.  Unexamined, inherited, and likely flawed perspectives can so easily propagate without the creation of viable alternatives for those they might negatively affect. 

Take, for instance, the idea of an event.  To whom does an event 'belong' and of what does it 'have many'?  One could say an event belongs to a creator and has many attendees.  An artist decides to have concerts and invites many people to attend.  Easy enough in activerecord:

```
#Database Migrations

class CreateEvents < ActiveRecord::Migration[5.1]
  def change
    create_table :artists do |t|
      t.string :name
    end
 
    create_table :events do |t|
      t.belongs_to :artist, index: true
      t.string :name
    end
		
    create_table :attendees do |t|
      t.belongs_to :event, index: true
      t.string :name
    end
  end
end

#Models

class Artist < ActiveRecord::Base
  has_many :events
end
 
class Event < ActiveRecord::Base
  belongs_to :artist
  has_many :attendees
end
 
class Attendee < ActiveRecord::Base
  belongs_to :events
end
```

This implies that events come in to being via artists and attendees come in to being via events.  An event-world has been created whereby all the power is in the hands of the artist.  The attendee doesn't have the ability to exist beyond the event, to become a 'fan' and go to many events, or to have many artists they follow.

Another way of seeing might acknowledge the producer or the venue as having some position of a management over events.  The venue is in service of artists and fans and contains many events.  ActiveRecord gives a couple ways to do declare these relationships: "has_and_belongs_to_many" and "has_many :through".

```
#Database Migrations

class CreateEvents < ActiveRecord::Migration[5.1]
  def change	
		create_table :venue do |t|
      t.string :name
			t.belongs_to :artist, index: true
			t.belongs_to :fan, index: true
    end
		
		create_table :events do |t|
      t.string :name
			t.belongs_to :venue, index: true
    end
		
    create_table :artists do |t|
      t.string :name
    end
		
    create_table :fans do |t|
      t.string :name
    end
  end
end

#Models

class Venue < ActiveRecord::Base
  belongs_to :artist
	belongs_to :fan
	has_many: events
end

class Event < ActiveRecord::Base
  belongs_to :venue
end

class Artist < ActiveRecord::Base
  has_many :venues
  has_many :events, through: :venues
	has_many :fans, through: :events
end
 
class Fan < ActiveRecord::Base
  has_many :venues
  has_many :events, through: :venues
	has_many :artists, through: :events
end
```

Here, though, it looks like fans are fans of the venue, not the artist themselves, and the event no longer seems to have any participants.  The event is not a thing that a can be searched through to find a list of who is attending or who is the artist.

Perhaps a more egalitarian mode my serve best.  An event no more belongs to the artist than it does to the venue proprietor or the fans themselves.  A concert could evaporate just as easily by a case of laryngitis or a foreclosure on the building or a mass change of heart due to some previously unrevealed scandal.  An event comes into being through an agreement by all the parties involved in its occurence to show up at a given time and partake in some prescribed or improvised way.  So one could say here that the event both has many and belongs to its participants.  It might look something like this:

```
#Database Migrations

class CreateEvents < ActiveRecord::Migration[5.1]
  def change
    create_table :participants do |t|
      t.string :name
    end
 
    create_table :events do |t|
      t.string :name
    end
		
    create_table :participants_events do |t|
      t.belongs_to :participant, index: true
      t.belongs_to :event, index: true
    end
  end
end

#Models

class Participant < ActiveRecord::Base
  has_many_and_belongs_to :events
end
 
class Event < ActiveRecord::Base
  has_many_and_belongs_to :participants
end
```

However, this is a very flat kind of event-universe where everyone looks the same.  A simple "Participant" class doesn't quite capture all the nuanced roles people take on when they find themselves at an event.  Perhaps a self join table would help to differentiate participant's relationships to each other.

```
#Database Migrations

class CreateEvents < ActiveRecord::Migration[5.1]
  def change
    create_table :participants do |t|
		  t.string :name
      t.references :artists, index: true
    end
 
    create_table :events do |t|
      t.string :name
    end
		
    create_table :venues do |t|
      t.belongs_to :participant, index: true
      t.belongs_to :event, index: true
    end
  end
end

#Models

class Participant < ActiveRecord::Base
  has_many :venues
	has_many :events, through: :venues
  has_many :fans, class_name: "Participant",
                                    foreign_key: "artist_id"
 
  belongs_to :artist, class_name: "Participant"
end
 
class Event < ActiveRecord::Base
  has_many :venues
	has_many :participants, through: :venues
end

class Venue < ActiveRecord::Base
  belongs_to :participant
	belongs_to :event
end
```

A polymorphic association could also exist as events could belong to more than one other model (venues and participants) 

```
#Database Migrations

class CreateEvents < ActiveRecord::Migration[5.1]
  def change
    create_table :events do |t|
      t.string :name
			t.references :eventable, polymorphic: true, index: true
    end
 
    create_table :participants do |t|
		  t.string :name
      t.references :artists, index: true
    end
		
		create_table :venues do |t|
      t.string :name
    end
		    
  end
end

#Models

class Event < ActiveRecord::Base
  belongs_to :eventable, polymorphic: true
	has_many :venues
	has_many :participants, through: :venues
end
 
class Participant < ActiveRecord::Base
  has_many :events, as: :eventable
	has_many :venues
	has_many :events, through: :venues
  has_many :fans, class_name: "Participant",
                                    foreign_key: "artist_id"
 
  belongs_to :artist, class_name: "Participant"
end
 
class Venue < ActiveRecord::Base
  has_many :events, as: :eventable
	belongs_to :participant
	belongs_to :event
end
```

Needless to say, things have gotten a little out of hand at this point.  I'm not even sure if that last one works, but I find it fun to dream up these associations and test them.  In the end, I decided to keep it simple since this is a tiny project, for the sake of learning, and never to be used by or have influence over a public audience.  

I created an events organizer in which a user can create events for themself and view them lumped together through the lens of [the four burner theory of work-life balance](https://jamesclear.com/four-burners-theory).  The scope of it doesn't allow users to invite others to the same event, or allow users to see anyone else's events.  When I tried to implement this kind of functionality I found myself creating a full on social network with calendars and both public and private events.   The task was just too great for this single assignment, and I must move on to discover rails.

But it really got me thinking!

