---
layout: post
title:      "React/Redux Project"
date:       2018-05-28 15:17:04 -0400
permalink:  react_redux_project
---


React is a powerful framework for JavaScript allowing for more simplicity when creating interactive user interfaces.  With the use of a virtual DOM and a declarative programming style it helps makes interaction speedy and intuitive.  Redux further extends the power of React by providing a predictable state container, which makes the web app more predictable and easy to test.  This project is, primarily, an exploration of those tools.  It will also utilize a Rails API backend to demonstrate a culmination of the skills acquired at Flatiron.  

## The App: 

The app is called "Pivot," and it's just the beginning of a set of tools to help people who are dissatisfied with their work life, confused about the idea of finding their "purpose," and interested in understanding what actually makes a person feel fulfilled by their job.  The philosophy behind these career tools can be found at [80000hours.org/](https://80000hours.org/), and the current tool in implementation can be used to help with [the question of job satisfaction](https://80000hours.org/career-guide/job-satisfaction/).  It's a form that allows users to compare a few different jobs based on a set of questions enumerated in the aforementioned article on job satisfaction.  The site is rendered as a series of components using React and their states are managed using Redux architecture.

### Challenges:

As I mentioned before one of the wonderful things about React is its use of declarative programming.  Declarative programming, as all us Flatiron students know, is programming that allows the programmer to describe *what* a program should accomplish rather than *how* it should do so.  Once I understood how to setup the redux architecture and the basics of react syntax there were very few challenges getting the browser to display just what I wanted, when I wanted it to.  However, one puzzle that took some maneuvering was getting my nested form to work both on the Rails backend and the React front end.

Creating a nested form can be a little complicated, even just staying inside Rails, and getting React to receive and render information that Rails could send and understand was no different.  The main hitch came when I started sending the Rails API information from my completed form.  Rails has some convenient methods, namely `form_for`, which will automatically send the correct class the correctly formatted and persist accordingly.  React gave me a little trouble mainly because I was nesting the same form via a different component three times in the parent form.  Because the parent did not contain any state information (other than to tell us to redirect once the form had been submitted) I needed to gather up the data using some getElement... functions.  Take a look at this snip of code: 

```
  handleSubmit(event) {
    event.preventDefault();

    const comparison = {predictors_attributes:[
      {career_id: 0, engagement: 0, altruism: 0, skill: 0, support: 0, basic_needs: 0, balance: 0},
      {career_id: 0, engagement: 0, altruism: 0, skill: 0, support: 0, basic_needs: 0, balance: 0},
      {career_id: 0, engagement: 0, altruism: 0, skill: 0, support: 0, basic_needs: 0, balance: 0}
    ]}

    const predictors = document.getElementsByClassName("PredictorForm")
    Array.prototype.forEach.call(predictors, function(predictorsElem, index) {
      const predictorInputs = predictorsElem.getElementsByClassName("predictorInput")
      Array.prototype.forEach.call(predictorInputs, function(inputElem) {
        if(inputElem.attributes.value){
          comparison["predictors_attributes"][index]["career_id"] = parseInt(inputElem.attributes.value.value) + 1
        }else{
          comparison["predictors_attributes"][index][inputElem.name] = parseInt(inputElem.value);
        }
      });
    });

    this.props.addComparison(comparison);
    alert("This Comparison Has Been Saved!")
    this.setState({ fireRedirect: true })
  }
```

It's not my favorite solution, but what I'm doing is taking all the information from my form and putting it into an object that my Rails controller can understand.  When Rails gets that `comparison` object it can easily clean the parameters via `params.permit(...).require(...)`, and feed them directly into the model's update function.  I've had to forgo some JS naming conventions while in JS so as not to spend a great deal of time converting the syntax.  What would be terrific is if I could simply store all this in a local state and send it along without scraping my form page.  I suppose I could do that by creating one long initial state constructor, and not setting up the nested portion of my form in another component.  I preferred this way, though, because it felt repetitive to duplicate the same state variables and form code three times over.

Another challenge came when I was attempting to format some information from an outside API into something that fit my sites purposes.  At times I would need to go through a nested JSON object and reform an array into a string that could be persisted via my SQL database, and sometimes I'd need to go in the other direction taking a string from the JSON sent and parsing it into an array I could properly map in a presentation component.  Take this little switch as an example: 

```
  if (career.length !== 0){
    whatTheyDo = career.what_they_do.split(".").map((descriptor) =>
      <div className="list-group-item list-group-item-action">{descriptor}</div>
    )

    aka = career.aka.split(",").map((aka) =>
      <div className="list-group-item list-group-item-action">{aka}</div>
    )

    onTheJob = career.on_the_job.split(".").map((responsibility) =>
      <div className="list-group-item list-group-item-action">{responsibility}</div>
    )
  }
```

This helped me to take comma and period separated lists and make them into arrays whose elements I could display individually in some pretty bootstrap formatting.

### Accomplishments :

My favorite thing about this project was simply getting to know the state, component, and lifecycle flows in React.  The more I have a chance to reiterate this pattern, the faster I think I'll get at creating React apps with beautiful and intuitive UI's.  It took me a long time to follow the flows of information this go around, but I feel that time was well spent.  I now have a better understanding of where information is and at what points in my code I have the ability to manipulate it.

The exercise of accessing external API's was also empowering.  It's absolutely incredible the kind of information that's already out there, and this assignment got me excited to find more.  So much of what we need to make useful tools and beautiful visualizations is already out there.  Now that I can speak this language, I can't wait to do my part in creating them.  

### Goals for the Future :

1. Build tools for all twelve parts of the 80,000hrs career guide and any other career philosophies that help those seeking a change to find positive, actionable steps for doing so.

2. Create the capacity for users to login and for their profiles, comparisons, and knowledge to be shared with others.  This might create a supportive community around the challenge of a career change.

3. Make the site's design more unique by either customizing Bootstrap more effectively or creating a CSS scheme all my own.

