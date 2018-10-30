# NodeJS-Express-Tutorial
https://www.codecademy.com/learn/learn-express

### CRUD
Create, Read, Update, and Delete

### Starting A Server
To create a server, the imported express function must be invoked.
```javascript
const express = require('express');
const app = express();
```
The purpose of a server is to listen for requests, perform whatever action is required to satisfy the request, and then return a response.
In order for our server to start responding, we have to tell the server where to listen for new requests by providing a port number argument to a method called app.listen().

The second argument is a callback function that will be called once the server is running and ready to receive responses.
```js
const PORT = 4001;
app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

### Routes
To tell our server how to deal with any given request, we register a series of routes. Routes define the control flow for requests based on the **request's path** and **HTTP verb**.

For example, if your server receives a GET request at '/monsters', we will use a route to define the appropriate functionality for that HTTP **verb (GET)** and **path (/monsters)**.

**Path**
The path is the part of a request URL after the hostname and port number, so in a request to localhost:4001/monsters, the path is /monsters (in this example, the hostname is 'localhost', the port number is '4001').

**Methods/verbs**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods

Express uses app.get() to register routes to match GET requests. Express routes (including app.get()) **usually take two arguments**, a **path (usually a string)**, and a **callback function to handle the request and send a response**.
```JS
app.get('/expressions', (req, res, next) => {
  console.log('Callback function!')
});
```

### Static Files
**Serving static files in Express**
Express, by default does not allow you to serve static files. You need to enable it using the following built-in middleware.
Static files are files that clients download as they are from the server.
The function signature:
```JS
express.static(root, [options])
```
The root argument specifies the root directory from which to serve static assets.
```JS
app.use(express.static('public'))
```
Note: There should be a "public" folder in your applications root folder. There you can put the static files.


### Sending A Response
HTTP follows a one request-one response cycle. Each client expects exactly **one response per request**, and each server should only send a single response back to the client per request.

Express servers send responses using the **.send() method** on the response object. **.send() will take any input and include it in the response body**.

In addition to .send(), **.json()** can be used to **explicitly send JSON-formatted responses**. .json() sends any JavaScript object passed into it.

```js
const monsters = [{ type: 'werewolf' }, { type: 'hydra' }, { type: 'chupacabra' }];
app.get('/monsters', (req, res, next) => {
  res.send(monsters);
});
```

### Route Paths
![Route Matching](https://github.com/shafix/NodeJS-Express-Tutorial/blob/master/Route%20matching.PNG)

### Dynamic Paths
Routes become much more powerful when they can be used dynamically. Express servers provide this functionality with **named route parameters**. 

Parameters are route path segments that begin with : in their Express route definitions. They act as **wildcards**, matching any text at that path segment. For example **/monsters/:id** will match both **/monsters/1** and **/monsters/45**.

Express **parses any parameters**, **extracts their actual values**, and **attaches them as an object to the request object**: **req.params**. This object's **keys are any parameter names** in the route, and each key's **value is the actual value of that field** per request.

Example:
```js
const monsters = { hydra: { height: 3, age: 4 }, dragon: { height: 200, age: 350 } };
// GET /monsters/hydra
app.get('/monsters/:name', (req, res, next) => {
  console.log(req.params) // { name: 'hydra' };
  res.send(monsters[req.params.name]);
});
```
Explanation:
In this code snippet, a .get() route is defined to match **/monsters/:name path**. When a GET request arrives for **/monsters/hydra**, the callback is called. Inside the callback, **req.params** is **an object with the key name and the value hydra**, which was present in the actual request path. The appropriate monster is retrieved by its name from the monsters object and sent back the the client.

### Status Codes
Express allows us to set the status code on responses before they are sent. Response codes provide information to clients about how their requests were handled.

The **res** object has a **.status()** method to allow us to set the status code, and other methods like **.send()** can be chained from it.
```js
const monsterStoreInventory = { fenrirs: 4, banshees: 1, jerseyDevils: 4, krakens: 3 };
app.get('/monsters-inventory/:name', (req, res, next) => {
  const monsterInventory = monsterStoreInventory[req.params.name];
  if (monsterInventory) {
    res.send(monsterInventory);
  } else {
    res.status(404).send('Monster not found');
  }
});
```

### Longer Route Paths
![Route Matching 2](https://github.com/shafix/NodeJS-Express-Tutorial/blob/master/Route%20matching%202.PNG)

### PUT requests
**PUT** requests are used for updating existing resources.

### Query strings
Query strings appear at the **end of the path in URLs**, and they are **indicated with a ? character**. For instance, in **/monsters/1?name=chimera&age=1**, the **query string** is **name=chimera&age=1** and the **path** is **/monsters/1/**
Query strings do not count as part of the route path.
Express server **parses them into an object** and attaches it to the request body as **req.query**.
The **key: value** relationship is **indicated by the = character** in a query string, and key-value pairs are **separated by &**.

Example:
```js
const monsters = { '1': { name: 'cerberus', age: '4'  } };
// PUT /monsters/1?name=chimera&age=1
app.put('/monsters/:id', (req, res, next) => {
  const monsterUpdates = req.query;
  monsters[req.params.id] = monsterUpdates;
  res.send(monsters[req.params.id]);
});
```
Here, we have a **route for updating monsters by ID**. When **a PUT /monsters/1?name=chimera&age=1 request** arrives, our callback function is called and, we **create a monsterUpdates variable to store req.query**. Since **req.params.id is '1'**, we **replace monsters['1']'s value with monsterUpdates** . Finally, Express **sends back the new monsters['1']**.

### Longer Route Paths
![HTTP verb matching](https://github.com/shafix/NodeJS-Express-Tutorial/blob/master/Matching%20by%20HTTP%20verb.PNG)
Express matches routes using both path and HTTP method verb.

### POST requests
**POST** is the HTTP method verb used for creating new resources. 
Because POST routes create new data, their **paths do not end with a route parameter**, but instead **end with the type of resource to be created**.
For example, to create a new monster, a client would make a **POST request to /monsters**. The client does not know the id of the monster until it is created and sent back by the server, therefore **POST /monsters/:id doesn't make sense** because a client couldn't know the unique id of a monster before it exists.
Express uses **.post()** as its method for POST requests. 
The HTTP status code for a newly-created resource is **201 Created**.

### DELETE requests
**DELETE** is the HTTP method verb used to delete resources.
Because DELETE routes delete currently existing data, **their paths should usually end with a route parameter** to indicate **which resource to delete**.
Express uses **.delete()** as its method for DELETE requests.
Servers often send a **204 No Content** status code if deletion occurs without error.
