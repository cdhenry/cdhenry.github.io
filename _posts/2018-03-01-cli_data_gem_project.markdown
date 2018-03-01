---
layout: post
title:      "CLI Data Gem Project"
date:       2018-03-01 17:36:27 +0000
permalink:  cli_data_gem_project
---


This turned out to be a challenging project, but not for the reasons I initially expected (which is great!).  I learned a ton about my process as a coder, and I'm evolving by creating more efficient modes of making.  This post is about my main take-away from the project.

Being more efficient means slowing down.

Up until this point I've been pretty capable of reading the lessons and executing the labs without spending much time planning or outlining how I'll move ahead.  There have definitely been moments of confusion, but those moments haven't taken me too far away from the instinctual direction with which my solutions began.  More than anything that's a testament to the clarity of the curriculum Flatiron has developed.  They've done such a seamless job of lesson planning that it feels like my instincts are leading the way unobstructed.  However, once the unpredictable nature of live websites, not necessarily created with the finest coding practices in mind, came into the mix, 'my instincts' were not so infallible.

What gave me the most trouble in this project was the parsing of data from a website.  The website I chose wasn't super-tidy, but it was full of information of interest to me.  On top of that, it was one of those annoyingly slow-loading websites due to advertisements and poor coding.  A gem that could quickly scrape the most relevant information and feed it to me in a minimalist fashion would be a total time-saver and annoyance-eliminator.  

Using nokogiri and css identifiers I started to collect data.  On many well-crafted sites the identifiers wrap up information in tidy boxes with nested in descriptive 'div' trees.  I imagine it's useful to make things modular in this way in case someone has to change a small element and doesn't want to send the entire site crashing down or spend their entire day looking for the one thing they need.  Often the information I wanted was embedded in cryptic or generic identification markers, and would likely be flattened into divs containing unrelated, unwrapped data.  I would go in for a particular piece and immediately have to deal with having collected either too much data or a block with untenable formatting issues.

As an example:  If I wanted to get a show's schedule of performances, rather than describing it to nokogiri with a css class or id or list marker such as:

`@schedule = doc.css(".show_schedule").text.strip` 

I ended up having to scrape the whole block of text it was it and use the string .index method to parse out the shows blurb, synopsis, and schedule (which would sometimes be called 'Show Times') like this:

```
info = doc.css(".bsp-bio-text").text.strip
last = info.length
show = self.new

    if info.include?("SCHEDULE")
      blurb_i = info.index /[.]\S/
      schedule_i = info.index("SCHEDULE")
      show.blurb = info[0..blurb_i]
      
      if show.blurb.include?("SYNOPSIS:")
        show.blurb.gsub!("SYNOPSIS: ", "")
      end
      
      show.schedule = info[schedule_i..last]    
    elsif info.include?("Show Times")
      blurb_i = info.index /[.]\S/
      schedule_i = info.index("Show Times")
      last_i = info.index("Tickets") - 1
      show.blurb = info[0..blurb_i]
      
      if show.blurb.include?("SYNOPSIS:")
        show.blurb.gsub!("SYNOPSIS: ", "")
      end
      
      show.schedule = info[schedule_i..last_i]
    end
```

Not my cleanest piece of code, but it does make sense of a disorganized website.

In this process, I also learned how useful nokogiri's '.parent' and '.children' methods are.  With them I was able to index many siblings "html (p)'s" under a single "div" and quickly parse through them.  I became very familiar with the '.each,' '.include?,' and '.index' methods to get after just the right pieces of information I needed.  

I think a more experienced coder would have recognized the issues with this website right off the bat and either chosen a different one or started coding with a more robust sense of the methods needed.  Slowing down in the beginning, creating an outline for where I'd be going, would have helped immensely both with my sanity and my efficiency.
