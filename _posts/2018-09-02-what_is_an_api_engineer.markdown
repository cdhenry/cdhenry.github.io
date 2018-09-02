---
layout: post
title:      "What is an API Engineer?"
date:       2018-09-02 15:05:49 -0400
permalink:  what_is_an_api_engineer
---

> In 1995 only the most tech savviest had websites, and by 2000 many businesses realized having a website was the normal way of doing business. In 2010 many of the tech savviest companies had APIs, and by 2017 many businesses are realizing that having an API is the normal way of doing business--imagine what the Internet will look like by 2020. - http://101.apievangelist.com/
> 

As the job search begins, I've noticed a few opportunties for the position of, 'API Engineer' and it got me wondering ...  

Going through the Flatiron School's program, I've, of course, gleaned that an API is a way for "one system to interact with another via a well defined interface", that an API defines the rules by which a programmer can interact with an application or database.  However, I was baffled by the idea that writing one could be an entire job for one programmer, much less a whole team.  Certainly, they are important.  They are the way that software engineers, and, increasingly, less technically inclined positions are forced to use to fetch data, make insights, and build applications.  But couldn't any old software developer simply use RESTful routes, give their keys relevant names, and call it a day?

Apparently not.  There is much more to designing a useable API, and these are just a few of the things I've learned this week researching the topic:

### What does an API Engineer Do?

There is an entire [economy around APIs](https://www.upwork.com/hiring/api-economy/) and organizations everywhere are trying to get involed.  An API engineer can take a company's aspirations in this regard and make them into something practical.  They must both interpret the vision of an organization's stakeholders and at the same time design smooth experiences for developers interested in building applications on their stack.  This requires not only an in-depth knowlege of programming concepts and trends, but also an ability to effectively communicate with all levels of an organization and its clients.

In regards to programming knowledge, an API Engineer, not only needs to be well-versed in REST and JSON, but also should be interested in alternative protocals such as [MQTT](http://mqtt.org/) or [Apache Thrift](https://thrift.apache.org/).  They should know which one and how best to use each protocal in realtion to a specific company's API needs.  They'll have to think about things like speed and security; they should know when and how best to reuse existing APIs, so as not to repeate functionality or to repeat functionality with certain limits; they should know how best to implent those limits and keep data secure.

In regards to communication, an API Engineer, should be able to think both architecturally and linguistically.  Documentation is such a huge part of an API that a developer will often go back and forth between being a programmer and being a technical writer.  The ability to tell a good story and document why and how decisions were made is cruicial to the success of an API.  An API can be efficient and well-constructed, but unless it's also compelling and delightful to the API consumer crickets will follow its implimentation.

### Characteristics of a Well Designed API

In general, there are three characteristics of a well designed API: ease of use (read/work with/update), inability to misuse, and completeness.  

Here are some more specific things to think about when designing an API:

> * Determine what types of resources an API provides.
> * Determine the relationships between resources.
> * Decide the resource name schemes based on types and relationships.
> * Decide the resource schemas.
> * Attach minimum set of methods to resources.
> - Resource Oriented Design, Google
> 

An API whose resources and associated properties are easy to memorize and facilitate incremental development are much more compelling to potential developers.  Practically, that might mean using only nouns to describe your resources, and making sure those nouns convey just the right amount of information about at resource and it's relationships.  It'll also mean providing good feedback to help developers succeed.

> Every client request and server side response is a message and, in an ideal RESTful ecosystem, these messages must be self descriptive. Good feedback involves positive validation on correct implementation, and an informative error on incorrect implementation that can help users debug and correct the way they use the product. For an API, errors are a great way to provide context to using an API. - [SwaggerBlog](https://swagger.io/blog/api-design/api-design-best-practices/)
> 

While an API should strive to provide all the required information for developer integration descriptively and deeply, API's are generally written in stages.  Figuring out an APIs common use cases can take time, and one should be careful about exposing too much unnecessary data.  APIs operate as a gate, allowing companies to share select information but also keeping unwanted requests out.

#### Resources:
[ProgrammableWeb](https://www.programmableweb.com/news/how-to-hire-great-api-developer/analysis/2016/05/19)

[SwaggerBlog](https://swagger.io/blog/api-design/api-design-best-practices/)
