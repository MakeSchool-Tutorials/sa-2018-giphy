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

>[action]
>
Inside the `ms-giphy` folder, tell NPM to install the packages our app needs by running
>
```
NPM install
```

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

Our first goal is to build an _MVP_–a _Minimum Viable Product_, or the simplest possible version of our app. In this case, our _MVP_ will be a single call to Giphy's random API endpoint, and a simple web page that displays the .gif it returns. Let's start by building the API call.

## Use Request to Call Giphy Random API

Let's open the `ms-giphy` directory in your text editor. You should have these files:

![file tree](assets/file_tree.png)

Open `routes/index.js`, and you should see the following code:

```Javascript
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;
```

Express has defined one route for us already at `/`, which is the root route of our web site-when somebody visits `www.my-awesome-site.com` (or `localhost:3000`, as we'll do in a moment), this is the function we'll call. At the moment, all it does is return a file called `index` with a title "Express"–we'll see what all this means in the next section. For now, let's set this function up to use Request

>[action]
>
```Javascript
const express = require('express');
const router = express.Router();
>
// Add this line
const request = require('request');
>
/* GET home page. */
router.get('/', function(req, res, next) {
>
  // Add the following 5 lines (be sure to add _your_ API key to the URL)
  const url = "http://api.giphy.com/v1/gifs/random?api_key=YOUR-API-KEY";
  request.get(url, (err, response, body) => {
    if(err) { console.error(err) }
>
    body = JSON.parse(body);
    console.log(body);
>
    res.render('index', { title: 'Express' });
  });
});
>
module.exports = router;
```
>
First, we are requiring Request and assigning it to the variable `request`–now we can access all of Request's functionality through that variable.
>
Next, we save the Giphy API Random Endpoint URL as a variable–this isn't totally necessary, but it makes it look a little nicer on the next line when we have to pass it in to `.get()` as an argument. Also, be sure to replace `YOUR-API-KEY` with _your API key_–otherwise you'll just get `422` errors instead of gifs.
>
Finally, we parse the `body` to convert it from a JSON string to a Javascript object, and log that to the console. (Here, `console.log()` will print messages in your terminal because this code runs on the server; if you run `console.log()` from inside a browser, it will print to your browser console.)

Let's try it out: in your terminal, make sure you're in your `ms-giphy` folder and type:

```
nodemon start
```

If you don't see an error message, open your browser and visit `localhost:3000`. You should see the default Express home page, something like this:

![Express Default Home Page](assets/express_home.png)

This is because we haven't set this page up yet–we'll do that next. But if you look at the terminal, you should see a couple of `GET` request logs and a rather large object with keys like `slug`, `url`, `bitly-url`, etc... If so, we're ready to display that result on a web page.

If you see `{ message: 'Invalid authentication credentials' }` instead, check your API key in the URL.

## Load result into view

At this point, when we visit the home page of our app we don't see anything interesting–but on the back end, we're making a call to the Giphy API and getting a random .gif. Let's display that back end work on a page.

Take another look at `routes/index.js`, especially the `res.render()` line:

```Javascript
res.render('index', { title: 'Express' });
```

This says that when someone hits our root route, `/`, we'll _render_ a file called `'index'`. That file happens to be located at `views/index.hbs`, so let's look at that file:

```HTML
<h1>{{title}}</h1>
<p>Welcome to {{title}}</p>
```

Notice the double-brace notation (`{{...}}`)–that's _Handlebars_. Here, Handlebars expects a variable called `title`, which we assign in our `routes/index.js`. Go back to that file, and in `res.render()`, let's change the title to something other than `'Express'`, like `'Make School Giphy'`:

```Javascript
res.render('index', { title: 'Make School Giphy' });
```

Then, if you refresh `localhost:3000` in your browser, you should see this:

![Make School Giphy Home Page](assets/ms_giphy_home.png)

Now that we can control what appears on our home page from the back end, let's display our .gif.

>[action]
>
Update `views/index.hbs` to look like the following:
>
```HTML
<h1>{{title}}</h1>
<p>
  <img src="{{imgUrl}}" />
</p>
```
>
Here, we're adding an `img` tag onto the page, but instead of a URL we have Handlebars tag that renders a variable `imgUrl`. Just like our `title` variable, we need to give it a value in `routes/index.js`.

>[action]
>
Open `routes/index.js`, and update the `res.render()` line to include a value for `imgUrl`:
>
```Javascript
const express = require('express');
const router = express.Router();
const request = require('request');
>
/* GET home page. */
router.get('/', function(req, res, next) {
  const url = "http://api.giphy.com/v1/gifs/random?api_key=YOUR-API-KEY";
  request.get(url, (err, response, body) => {
    if(err) { console.error(err) }
>
    body = JSON.parse(body);
>
    // Add this line to get the .gif's URL from the Giphy response body:
    const imgUrl = body.data.source_image_url
>
    // And pass it to our view as imgUrl:
    res.render('index', { title: 'Make School Giphy', imgUrl: imgUrl });
  });
});
>
module.exports = router;
```

Now refresh 'localhost:3000' again, and you should see a random .gif:

![Make School Giphy First .gif](assets/ms_giphy_first_gif.png)

If you see a broken image icon instead of a .gif, try refreshing the page–our app is still very simple and lacks error handling, and some .gifs only work in specific situations.

![Make School Giphy Broken Image](assets/ms_giphy_broken.png)

## Add CSS

One last step before we finish our _MVP_, let's add some styling. We won't spend a lot of time making our app beautiful, but it's easy to make it look pretty good really fast. [Bootstrap](https://getbootstrap.com/) is a popular front end framework, and it's very easy to set up in three steps: First, we'll include Bootstrap in our project; Second, we'll add some Bootstrap classes to our HTML; Third, we'll add some custom CSS.

>[action]
>
Start by opening the `views/layout.hbs`, and add the line indicated below:
>
```HTML
<!DOCTYPE html>
<html>
  <head>
    <title>{{title}}</title>
>
    <!-- Add the following 3 tags: -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
>
  <!-- And add these classes to the body: -->
  <body class="container-fluid text-center">
    {{{body}}}
  </body>
</html>
```
>
This will download CSS files from a Bootstrap server instead of our own server, which means we don't have to do anything else to set it up–we're ready to start using it.

>[action]
>
Next, go back to `views/index.hbs`, and update your HTML to match the following:
>
```HTML
<div class="row">
  <div class="col-lg-1">
    <h1>{{title}}</h1>
  </div>
</div>
>
<div class="row">
  <div class="col-lg-1">
    <img src="{{imgUrl}}" />
  </div>
</div>
```

>[action]
>
Finally, let's take care of some custom CSS. Open the file `public/stylesheets/style.css`. Delete everything inside, and add the following:
>
```CSS
body {
  padding: 2em;
}
```

Now if you refresh `localhost:3000`, you should see something like the following:

![MS Giphy Styled](assets/ms_giphy_styled.png)

Congratulations, we completed our MVP! Our app fulfills a basic function–fetching a random gif from the Giphy API–and it doesn't do _anything_ else. Let's add a search feature.

# Giphy Search

So far, when a user visits our app, we make a call to Giphy's `/random` API endpoint and display the result to the user. It's very simple, but it isn't interactive at all (beyond letting the user refresh the page...). Our search feature will take input from the user–what kind of .gif do they want?–which means we have to get that input and handle it appropriately.

We'll add our search feature in three steps.  First, we'll create a search form where the user can enter their input. Next, we'll set up the server to handle the input from that form–that includes accepting the user's input, and making a call to Giphy's `/search` API endpoint. Finally, we'll display the results to the user.

## Giphy Search Form

>[action]
>
Create a new file in your `views` folder called `search.hbs`. Add the following code:
>
```HTML
<form action="/search" method="POST">
  <input type="text" name="giphy-query" placeholder="Search Giphy..." />
  <input type="submit" value="Search" />
</form>
```

In order to render that form, we need to set up an endpoint at `/search`.

>[action]
>
Open the `routes/index.js` file and add a GET route for `/search`, as below:
>
```Javascript
const express = require('express');
const router = express.Router();
const request = require('request');
>
router.get('/', (req, res, next) => {
  const url = "http://api.giphy.com/v1/gifs/random?api_key=7eJ7JRkWzxL5X7b1BqsjkOr28hX3rgDe";
  request.get(url, (err, response, body) => {
    if(err) { console.error(err) }
>
    body = JSON.parse(body);
    const imgUrl = body.data.source_image_url
>
    res.render('index', { title: 'Make School Giphy', imgUrl: imgUrl });
  });
});
>
// Add the following 3 lines:
router.get('/search', (req, res, next) => {
  res.render('search');
})
>
module.exports = router;
```
>
This action looks for a file called `'search'` in our `views/` folder–which we just created–and renders that file for the user.

Let's try it out by going to `localhost:3000/search` in your browser. You should see something like this:

![Giphy Search Form](assets/giphy_search_form.png)

## Giphy Search API

Now let's handle the user's input. Take another look at the first line of `views/search.hbs`:

```HTML
<form action="/search" method="POST">
```

What happens when a user submits this form? The browser sends a POST request with all the form data to the URL specified in `action`–in this case, `/search`. Let's define an endpoint to handle POST requests to `/search`.

>[action]
>
Open the `routes/index.js` file, and add the following router action:
>
```Javascript
router.post('/search', (req, res, next) => {
  console.log(req.body);
  res.status(204).send({});
});
```
>
The body of the HTTP request (`req.body`) contains the contents of the user's form, and we want to print them to the server terminal to make sure it works. Then we return an empty object just to close the request-response cycle.

In your browser, go to `localhost:3000/search`. Enter a search term, click the 'Search' button, and... hopefully nothing happens–That's what we expect. But look at the server logs in your terminal, and the second line from the bottom should be

```
{ 'giphy-query': 'YOUR SEARCH QUERY' }
```

That confirms that we can get the input from the user, but the trick here is relaying that search to the Giphy API. If you need a refresher on how the Giphy `/search` endpoint works, [you can check out the docs here](https://developers.giphy.com/docs/#operation--gifs-search-get).

<!-- TODO: point out that we're sending a GET request, not POST, and add info box about the two ways to send params (POST body or URL query params). Also explain why param is called 'giphy-query' and where defined -->

>[action]
>
In `routers/index.js`, update the `POST /search` action to the following:
>
```Javascript
router.post('/search', (req, res, next) => {
  const query = req.body['giphy-query']
  const url = `http://api.giphy.com/v1/gifs/search?api_key=YOUR-API-KEY&q=${query}`;
>
  request.get(url, (err, response, body) => {
    if(err) { console.error(err) }
>
    body = JSON.parse(body);
    console.log(body);
>
    res.status(204).send({});
  });
});
```
>
Unlike our request to `/random`, the URL we use to connect to the Giphy API will be different depending on the user's input. (Don't forget to replace `YOUR-API-KEY` in the URL above with your _real_ API key). In the first line we extract the user's query from the request body, and in the next we insert (or _interpolate_) it into the URL. But the rest is very similar to hitting the `/random` endpoint–we use `request` to connect to the Giphy API and we convert the response's body from JSON into a plain old Javascript object.

We're not displaying any results to the user yet, but we should be able to see something interesting if we look at the server output. Once more, go to `localhost:3000/search` in your browser, enter a search term, and click "Search". Again, you shouldn't see anything happen here, but if you look at your terminal, you should see a rather large response from Giphy. If so, we're ready to move on to parsing that result and displaying the results to the user.