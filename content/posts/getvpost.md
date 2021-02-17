---
title: "my GET vs. POST blog post"
date: 2021-02-3T23:04:08-06:00
draft: false
tags: ["http", "requests", "response", "api"]
categories: ["web development", "nodejs", "express"]
---

## GET vs. POST and REST APIs

Over the past month, I have worked on building web applications using node and express. You can check out the repo [here](https://github.com/collinholmquist/coffee-tasting-journal) ☕

My goal has to been to learn more about full-stack development and deepen my understanding on APIs, databases, and Node.js.  I skirted around with tutorials and small projects that involve (usually) front-end but have never bothered to connect any back-end with it.  I used my COVID-induced remote and more open spring and summer to learn React, Gatsby and eventually Node.js.  I worked on some of the content from Andrew Mead's Udemy courses over Node.  He provided me with some great learning experiences to get me working towards developing my own node applications with a working database and front-end. In November and December I began to create a dad-joke web app using node, express, mongodb and ejs.  I got the bare bones from one of the many tutorials out on the web and worked to customize it to fetch dad jokes and then implement basic CRUD (Create, Read, Update, Delete) operations.  A lot of web applications (and enterprise applications) boil down to communicating with databases via APIs.  What I didn't realize as I was working on the dad joke app was that I was developing an API.  

### What exactly are APIs? 

My prior experience with APIs were related to fetching data from another website: i.e. weather, location, etc.  Fetching a dad joke or list of jokes was within that limited scope of comprehension.  What I didn't realize was that creating routes in express was developing my own API for my app.  I am making the decisions of how my own web app interacts with data. By deciding how my app handles different request on different routes, I am making an interface for interacting with data. Additionally, I could have structure it so that other users could interact with my data as well, which could result in a special route (ex. api.github.com)It was a 'well of course' moment that connects all the little pieces of information in your brain and creates a bigger and better story.  It was the transition from being a recipient of data to delegator of data by designing the back-end of a web app.  

I made this connection when I was working on the following routes in my coffee journal app: 

```javascript
router.post('/entry', entries.create)
router.get('/journal', entries.findAll)
router.post('/journal', entires.orderBy)
```
⬆️ I implemented a router in express to make my code more modular...which would be worth another blog post! 

I was confused on why there was a need for different request types and different routes.  To me, `/journal` was just part of the url that would take you a different part of the a website.  When it was really part of the API I was designing.  To navigate to `mywebsite.com/journal` meant that I was sending a GET request to my server to retrieve a resource for the route `/journal` which in my case was tied to a webpage.  Here's where I had my eyes opened to what I really doing:  In my model for a journal entry, I had the following code for a post request to create an entry:

```javascript
exports.create = (req, res) => {
    //code not shown creates a new entry, passes it to method that queries database to create a new entry
    Entry.create(newEntry, (err, data) => {
        if(err){//handle error
        }

        else{
            res.send(data)
        }
    })
}
```
I had been browsing tutorials and express documentation that had been testing using postman, so I saw this `res.send(data)` a lot. When I would test my code and submit the form that wouldn't stay on the webpage, it would take me to the json that was being sent.  
So: 

```json
{
    "Coffee_ID": 1,
    "Roaster": "Archetype",
    "Region": "Kenya",
    "Tasting_Notes": "Floral, Citrus",
    "Brew_Method": "Chemex",
    "Comments": "Dry Finish"
}
```

I was confused to why this happening.  I realized that I was had programmed that route to send the data, when what I really wanted was the data to be sent to the webpage and then rendered.  Once I figured this out, everything I was doing became clearer and I was able to begin making intentional decisions to how I wanted my web application to operate.  

### GET vs. POST request

Another idea that became clearer was differentiating between GET and POST requests.  The basic details made sense:

- GET requests should only retrieve data
- POST requests are used to submit information that cause change in state or server changes

Deciding on uses for each in my API proved more difficult than I thought.  I wanted to get the my database entries in ascending order but thought that a get request would work since my understanding was that I didn't need to add any new entries.  I needed to send the name of the table that my data would be sorted by.  I was then planning to use the request's body to add which table (afterall, it had worked when I had made a post request), yet the `req.body` was always undefined when I printed it.  

Reading deeper into each request verb: 

- GET requests should not include data with the request.  It also does not include a body with the request.  
- POST requests have a body when used with middleware like `body-parser`

Ah! 💥 GET requests carry no body.  So only post requests will carry a body (and also a params which is related to the route) which will carry the data submitted in the request.  

And... I finally learned what a REST API is.  I found this great article from one of my favorite bloggers/developers Zell Liew: [Understanding and Using REST APIs](https://smashingmagazine.com/2018/01/understanding-using-rest-api/) 

>"REST determines how the API looks like. It stands for Representational State Transfer.  It is a set of rules that developers >follow when they create their API.  One of these rules states that you should be able to get a piece of data (called a resource) >when you link to a specific URL.  Each URL is called a *request* while the data sent back to you is called a *response*." 


