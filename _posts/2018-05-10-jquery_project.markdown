---
layout: post
title:      "jQuery Project"
date:       2018-05-10 09:53:39 -0400
permalink:  jquery_project
---


JavaScript, jQuery, and AJAX make using the web feel much more alive and interactive.  The ability to load and shift data without locking the browser into a synchronous call and response pattern can make a website feel almost as dynamic as a desktop.  This project gave us a chance to refactor a previous rails-only project into a site that utilizes JavaScript to make asynchronous calls. 

## The App and The Updates:

The previously created ["Road Trip" app](http://cdhenry.com/rails_project) is an application that allows users to create and share road trips.  The jQuery updates allow a user to comment on road trips and destinations, show and hide an index of comments on each page, and tab through individual pictures and tags on various pages all without reloading the browser.  When making the original application I wanted to challenge myself to juggle a deep set of associations, inside a complex application.  JavaScript and AJAX are complicated enough on their own that I decided to implement them in fairly straightforward ways.  Even so, the updates give the website new life. 

### Comments and Pictures:

I wanted to make many different kinds of objects commentable and imageable, so the most sensible thing seemed to be to create a couple polymorphic associations on the backend.  I gave the user the ability to add pictures and comments to road trips and destinations.  Additionally, I gave them the ability to add pictures of themselves to their own profiles.  Once those associations were created I wanted to use jQuery/AJAX to render an index of the objects.  It didn't make much sense to create new pages altogether for that list, nor would it be useful to render all comments or all pictures at once since they were associated polymorphic-ly.  However, with the help of nested routes, I was able to grab smaller groups of objects associated with more relevant parent nodes.

First, I created this giant document ready function with several click event listeners and AJAX calls.  The calls would grab some JSON created via ActiveModel Serialization and append them to the page.  Once I got it all working, not only did I notice everything blending together in this giant blob of a function, but I began to see a few repetitious blocks of code.  I found that by creating JavaScript Model Objects from the AJAX responses and separating my code into discrete bits of logic I was able to make it much more readable and less repetitious.

Lastly, I had to create several different prototypes on my comment JS model object in order to render the comment objects for different purposes.  For instance, on the road trip and destination show pages I wanted to render an index of all the comments on those objects with just the author name and title, but on the user pages I wanted to render all the comments that user had made and received with the kind of object they had commented on, the object's title, and the comment made.

```
Comment.prototype.showIndexComment = function() {
  return `<li>${this.author.name} : ${this.body}</li>`
}

Comment.prototype.showCommentMade = function () {
  return `<li>${this.type_object.name} (${this.commentable_type.split(/(?=[A-Z])/).join(" ")}) : ${this.body}</li>`
}
```

### Tags:

Taking a cue from the comments and pictures challenge, I began to create JS Model Objects and separate functions right from the get go when creating tags.js.  With tags I wanted users to be able to easily click through them without reloading the index page, and I wanted all the details available on the show page to load without a synchronous request.  Here, it made sense to paginate with jQuery and an Active Model Serialization JSON Backend.  

I gave the page a next and previous link.  The JS would listen for those clicks, highjack the events with a JSON get request, and create Tag JS model objects with the responses.  What was great about separating these steps into individual functions was that I noticed I could use a few of the same event listening functions on pictures as well.  This made paginating the picture objects that much more efficient.  

```
const setAttributes = function (id){
  $(".js-next").attr("data-id", id);
  $(".js-previous").attr("data-id", id);
  $(".js-page-count").text(`(${id + 1})`)
}

const getNext = function(getObject){
  $(".js-next").on("click", function(e) {
    let total = parseInt($(".js-next").attr("data-total"))
    let id = parseInt($(".js-next").attr("data-id"))
    if (total > id){
      var nextId = id + 1;
      getObject(nextId);
    }
    e.preventDefault();
  });
}

const getPrevious = function(getObject){
  $(".js-previous").on("click", function(e) {
    if (-1 < parseInt($(".js-previous").attr("data-id"))){
      var previousId = parseInt($(".js-previous").attr("data-id")) - 1;
      getObject(previousId);
    }
    e.preventDefault();
  });
}
```

### Hazards :

The toughest part of this assignment was creating a next/previous function that would work with both pictures and tags.  The tags were fairly easy to create a function for because I did not have to render the tags as nested resources, and I could simply increment the data-id one at a time.  However, with pictures I needed to scroll through just the pictures associated with the parent object.  One-by-one would not work here.  I came up with a couple solutions to this issue: 

1. I could grab the index of associated pictures and create an array of the indices associated.  Each time the next button was clicked the next array item could be accessed.  

2. I could scrap the next pagination idea for pictures all together and create a link similar to the load comments link.  So here users could show and hide the rest of their photos, and click on an individual photo to enlarge it.

Neither of these solutions were satisfying for me, so in the end I separated the click events for each object, and as of now picture.js looks like this:

```
$(function(){
  $(".js-next-pic").on("click", function(e) {
    $.get(this.href).success(function(data) {
      var counter = parseInt($(".js-next-pic").attr("data-id")) + 1;
      if (counter < data.length){
        $(".image_url")[0].src = data[counter]["url"] + new Date().getTime();
        // re-set the id to current on the link
        $(".js-next-pic").attr("data-id", counter);
        $(".js-previous-pic").attr("data-id", counter);
        $(".js-page-count").text(`(${counter + 1})`)
      }else {
        counter = parseInt($(".js-next-pic").attr("data-id")) - 1;
      }
    });
    e.preventDefault();
  });

  $(".js-previous-pic").on("click", function(e) {
    $.get(this.href).success(function(data) {
      var counter = parseInt($(".js-previous-pic").attr("data-id")) - 1;
      if (counter > -1){
        $(".image_url")[0].src = data[counter]["url"] + new Date().getTime();
        // re-set the id to current on the link
        $(".js-next-pic").attr("data-id", counter);
        $(".js-previous-pic").attr("data-id", counter);
        $(".js-page-count").text(`(${counter + 1})`)
      }else {
        counter = parseInt($(".js-previous-pic").attr("data-id")) + 1;
      }
    });
    e.preventDefault();
  });
});
```

Here I'm incrementing the hash received with a counter.  I'll need to separate my concerns here in to discrete functions, and hopefully during that process I'll find a more satisfying way to implement pagination.

### Accomplishments :

I'm really pleased with my comment.js on this project right now.  It's very easy to read and update if I wanted to add comments to other parts of the site or display them in more specific ways.  I'm also excited by this set_model definition in my polymorphic controllers:

```
  def set_model
    key = params.keys.last
    model = key.tr('_', ' ').chomp(" id").split.map(&:capitalize).join('')
    @model = eval model + ".find(params[:#{key}])"
  end
```

This kind of meta-programming always makes me feel like I'm accomplishing a lot with a very little code.  It basically functions as a set_params method but sets it to whichever model is currently associated with the comment or picture. 

### Goals for the Future:

1. I'd like to render more of my front-end with JS in order to get to know this language more intimately.  I'd also like to spend a little time reading something like 'Eloquent JavaScript' to get my code DRY and easily readable by other coders. 

2. Testing with JS is important too!  I'm setting an intention to learn the syntax for a JS test in addition to Ruby tests.

