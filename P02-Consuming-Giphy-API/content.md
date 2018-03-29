---
title: "Consuming the Giphy APIs"
slug: consuming-giphy-api
---

<!-- TODO:Introduction -->

# Setting Up Our Environment

<!-- TODO: consider creating a starter repo, and having students pull that down instead of going through following steps -->

<!-- ## Install Node and NPM -->
<!-- This tutorial should assume that students have already installed Node and NPM (and Express) -->

## Generate Template

Express can generate most of the files we need to build our app automatically if we install the `Express Generator`.

>[action]
>
In your terminal, type:
>
```
npm install express-generator -g
```

After the package finishes installing, we can generate a _skeleton_ for our app.

>[action]
>
In the terminal, enter:
>
```
express --hbs ms-giphy
```

There should be a new `ms-giphy` folder–you can type `ls` to see the contents of your current directory. Let's open that folder by typing `cd ms-giphy`.


## Add Extra Packages

We'll install two more packages before we move on–an _HTTP client_, and a development tool. In a previous section we used Postman to make HTTP requests, and an _HTTP client_ will let us do the same thing with code. Javascript actually has a built in HTTP client, but it's not very easy to work with, and as a result there are a bunch of popular HTTP packages on NPM. We'll use a package called [Request](https://github.com/request/request).

The other package we'll install is called [Nodemon](https://nodemon.io/) (short for _Node Monitor_). Any time we save changes to any of our files, Nodemon will restart the server for us automatically–this is a _massive_ convenience during development.

>[action]
>
To install Request and Nodemon, enter the following into the terminal:
>
```
npm install request nodemon --save
```

Now we're ready to build an app that calls the Giphy API.

# Getting a Random GIF

<!-- TODO -->

## Use Request to Call Giphy Random API

## Load result into view



# Giphy Search

After the core content, summarize what student should know now. Also link to the solution repository.

Finish the tutorial by providing links and resources for the student to dive deeper into the subject.
