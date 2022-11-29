# Lesson 7 - Web APIs

An API is an **A**pplication **P**rogramming **I**nterface that allows a web browser to access information from another web server. 

What does that mean???

Think about it the same way we did with Overpass - we are asking the other website a question to get an answer. 

**Example:** You could be searching your local library for a book. When you hit "search" in the bar, you are sending an API request to their internal database to see if the book is available. The library will return informaiton to your browser, including if the book is available and at which branch you can reserve it.

While there are a few different APIs, we are going to focus on REST (Representational State Transfer) APIs. REST defines the set of functions described in the example above (GET, PUT, DELETE) that clients (the browser or software the user is accessing) use to access server data. Clients and servers exchange data using HTTP/HTTPS.

REST uses a concept of _statelessness_ which means that servers do not save any client data between requests.

Let's look at some sample API requests!

1. https://api.github.com/users/sisskind (and you can replace "sisskind" with your github user name).
  a. The response will be a JSON that will include your user name, different URLs, and when your account was created (amongst other information)
2. http://sports.core.api.espn.com/v2/sports/football/leagues/nfl/seasons/2022/teams/2?lang=en&region=us
  a. This is an API from ESPN that gives you all the information about the Buffalo Bills in a JSON
3. https://catfact.ninja/fact
  a. This returns a fact about a cat. See...there is an API for everything.
  
The JSON responses can be used in a website by taking the properties and displaying them in a browser or a map...like we can do with [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API)! We have already created features in OpenStreetMap and queried it in the Overpass Turbo web application. Now we are going to allow users to query data directly in the application and have it added to the map. We will be using the same query language as you do in the Overpass Turbo application, except we will wrap it into a web request.

You can find the function in `index.html` around Line 94 (search for `var button2`). If you are looking at the webpage, this is the process to create the second button on the left-hand side of the page.

The query action will start when the user "clicks" the button, so we will add our code to the `L.DomEvent.on(button2, 'click', function(){` code block.

First thing we need to do is make sure we are not going to request too much data at once. We do this using an `if` statement to ensure that the zoom level is greater than 14

First, we want to check the zoom level of the map to make sure that we are not going to "lock up" the browser by requesting too much data. _(Take a quick break and learn more about zoom levels [here](https://leafletjs.com/examples/zoom-levels/)!)_ We will check the map zoom level using a built-in Leaflet function (`map.getZoom()`) and if it is less than 14, we will alert the user to zoom in and exit the function.

If not...the user gets to run a query! The default query is `["building"="yes"]`. We will come back to that in one second...

If the user submits an empty query (`if (query == null || query == "")`), it will exit the function as well.

So - let's assume that the user has created an actually query (using buildings above). We now do three things:
1. Get the bounds of the map to use in our query: `var bounds = map.getBounds();`
2. Create the URL that we are going to use for our query against the Overpass API: `var overpassURL` = `https://overpass-api.de/api/interpreter?data=[timeout:25];(way${query}(${bounds._southWest.lat},${bounds._southWest.lng},${bounds._northEast.lat},${bounds._northEast.lng});); out body;>;out skel qt;`
  * `https://overpass-api.de/api/interpreter?` is the URL link
  * `data=[timeout:25];` tells Overpass API to cancel after 25 seconds if the query does not go through
  * `(way${query}(${bounds._southWest.lat},${bounds._southWest.lng},${bounds._northEast.lat},${bounds._northEast.lng}););` is the actual query for any `ways` in Overpass that meet the query (this one being `"buildings"="yes"`).
3. Pass the query to the function that will request from Overpass API and return results as a map layer: `var outdata = getXML(overpassURL, layerControl);`
  * `getXML` is a function is `spark.js` and uses AJAX (Asynchronous JavaScript And XML) to make the request. We will talk more about this another time, but for now, just know that the request is made from our client and we wait for a response, and `error` or a `success`.
  * If we get an error, the function provides instructions for what to do - provide an alert that an error occurred with the `status text` from the server.
  * If we get a success response, the function provides instructions for that too! We will take the response from Overpass and covert the OSM to a GeoJSON (using the `osmtogeojson` function) and check for results. Assuming there is at least one feature (check out the if statement!), we will create a new layer and add it to the map.

**Okay...now for some fun**

1. Open up the map and before you zoom in, try clicking the "Query Overpass Turbo" button (looks like a steering wheel). You shoud get the warning about zoom levels.
2. Zoom in close to the map and try again. You should be able to run the buildings query, get a response of "Your layer has been added to the map!" and see the features.
3. Try querying different features...like sidewalks and lampposts! You should be able to use the same queries from before:
  * `["footway"="sidewalk"]`
  * `["highway"="street_lamp"]
4. If you run the street_lamp...you may come up empty because (if you remember), we were only querying for ways, not nodes. Try changing `way` to `node` in the function and query again. Do you get different results? Make sure that you are querying in an area where there are lamp posts (Compton Hill Reservoir Park has some).
  * How do you think we can update the web app? Should we always search for nodes and ways? Should we let the user choose? 
  * What about styles? Should we use the default list or let the user decide what color the incoming features should be?
  * How would we implement this? Try designing an idea before we meet again.
5. Last - let's see how many features actually are created. In the `if` statement in `getXML`, change the alert when a layer is added to the map to say how many features were added. You should be able to use something in that function to know how many features there are.
  * HINT --> https://github.com/sisskind/spark-map/blob/main/spark.js

If you have time, play around with the text and the defaults in the queries.

Sources:
* https://aws.amazon.com/what-is/api/
