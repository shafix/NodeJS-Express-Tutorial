# NodeJS-Express-Tutorial
https://www.codecademy.com/learn/learn-express

# Other useful links
https://nodejs.org/en/docs/guides/anatomy-of-an-http-transaction/
About requests:
- URL 
- Method/verb
- Header
- Body
- Errors
About responses:
- Status codes
- Response headers
- Response body
- Response errors
Other: 
- Some good examples
- Piping
# 

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
```js
app.post('/expressions', (req, res, next) => {
  newExpression = createElement('expressions',req.query);
  if (newExpression != false)
    {expressions.push(newExpression);
     res.status(201).send(newExpression); }
  else { res.status(400).send(); }
});
```

### DELETE requests
**DELETE** is the HTTP method verb used to delete resources.
Because DELETE routes delete currently existing data, **their paths should usually end with a route parameter** to indicate **which resource to delete**.
Express uses **.delete()** as its method for DELETE requests.
Servers often send a **204 No Content** status code if deletion occurs without error.
```js
app.delete('/expressions/:id', (req, res, next) => {
  const expressionIndex = getIndexById(req.params.id, expressions);
  if (expressionIndex !== -1) {
    deletedElement = expressions.splice(expressionIndex,1);
    res.status(204).send(req.params.id); } 
  else { res.status(404).send(); }
});
```


# Using routers
### Using routers can help your application be more module and less cumbersome.
An **Express router** provides a subset of Express methods. To create an instance of one, we **invoke the .Router() method** on the **top-level Express import**.
To use a router, we **mount it** at a certain path using **app.use()** and **pass in the router as the second argument**. This router will now be used for all paths that begin with that path segment. To create a router to handle all requests beginning with /monsters, the code would look like this:
```js
const express = require('express');
const app = express();

const monsters = {
  '1': {  name: 'godzilla', age: 250000000 },
  '2': {  Name: 'manticore', age: 21  }
}

const monstersRouter = express.Router();

app.use('/monsters', monstersRouter);

monstersRouter.get('/:id', (req, res, next) => {
  const monster = monsters[req.params.id];
  If (monster) { res.send(monster); } 
  else { res.status(404).send();
  }
});
```
Inside the **monstersRouter**, all matching routes are assumed to have **/monsters** prepended, as it is **mounted** at that path. **monstersRouter.get('/:id')** matches the full path **/monsters/:id**.
When a **GET /monsters/1** request arrives, Express **matches /monsters in app.use()** because the beginning of the path ('/monsters') matches. Express' route-matching algorithm enters the monstersRouter's routes to search for full path matches. Since **monstersRouter.get('/:id)** is **mounted at /monsters**, **the two paths together match the entire request path (/monsters/1)**, so the route matches and the callback is invoked. The 'godzilla' monster is fetched from the monsters array and sent back.

### Using Multiple Router Files
Generally, we will keep each router in its own file, and require them in the main application. This allows us to keep our code clean and our files short.
```js
// monsters.js
const express = require('express');
const monstersRouter = express.Router();

const monsters = {
  '1': { name: 'godzilla', age: 250000000 },
  '2': {  Name: 'manticore', age: 21  }
}

monstersRouter.get('/:id', (req, res, next) => {
  const monster = monsters[req.params.id];
  if (monster) { res.send(monster); } 
  else { res.status(404).send();
  }
});

module.exports = monstersRouter;
```
This code contains all the monsters specific code. In a more full-fledged API, this file would contain multiple routes. To use this router in another file, we use **module.exports** so that **other files can access monstersRouter**. The only other new line of code required is that Express must be required in each file, since we'll need to create a router with express.Router().

Our main.js file could then be refactored to import the monstersRouter:
```js
// main.js
const express = require('express');
const app = express();
const monstersRouter = require('./monsters.js');
app.use('/monsters', monstersRouter);
```

### Matching in nested routers
![Route Matching 2](https://github.com/shafix/NodeJS-Express-Tutorial/blob/master/Matching%20In%20Nested%20Routers.PNG)
Routers can be nested as many times as necessary for an application, so understanding nested route matching is important for created complicated APIs.




# Extra : DRY (Don't repeat yourself) code quality

### DRYing Routes With app.use() - Middleware
Middleware is code that executes between a server receiving a request and sending a response. It operates on the boundary, so to speak, between those two HTTP actions.
In Express, middleware is a function. Middleware can **perform logic on the request and response objects**, such as: 
inspecting a request, 
performing some logic based on the request, 
attaching information to the response, 
attaching a status to the response, 
sending the response back to the user, 
or simply passing the request and response to another middleware. 
Middleware can do any combination of those things or anything else a Javascript function can do.
```js
app.use((req, res, next) => {
  console.log('Request received');
});
```
The previous code snippet is an example of middleware in action. app.use() takes a callback function that it will call for every received request. In this example, every time the server receives a request, it will find the first registered middleware function and call it. In this case, the server will find the callback function specified above, call it, and print out 'Request received'.

### Using next()
We mentioned that most of Express's functionality is chaining middleware. This chain of middleware is referred to as the middleware stack.
The middleware stack is processed in the order they appear in the application file, such that middleware defined later happens after middleware defined before.
```js
app.use((req, res, next) => {
  console.log("A sorcerer approaches!");
  next();
});

app.get('/magic/:spellname', (req, res, next) => {
  console.log("The sorcerer is casting a spell!");
  next();
});

app.get('/magic/:spellname', (req, res, next) => {
  console.log(`The sorcerer has cast ${req.params.spellname}`);
  res.status(200).send();
});

app.get('/magic/:spellname', (req, res, next) => {
  console.log("The sorcerer is leaving!");
});

// Accessing http://localhost:4001/magic/fireball 
// Console Output:
// "A sorcerer approaches!"
// "The sorcerer is casting a spell!"
// "The sorcerer has cast fireball"
```
In the above code, **the routes are called in the order that they appear in the file**, provided the previous route called **next()** and thus **passed control to the next middleware**.
An Express middleware is a function with **three parameters: (req, res, next)**. The sequence is expressed by a set of callback functions invoked progressively after each middleware performs its purpose. **The third argument to a middleware function, next, should get explicitly called as the last part of the middleware's body**. This **will hand off the processing of the request and the construction of the response to the next middleware in the stack**.

### Routes as middleware
Express routes are middleware. Every route created in Express is also a middleware function handling the request and response objects at that part of the stack.

## Route-Level app.use() - Single Path
This is the app.use() function signature: 
```js
app.use([path,] callback [, callback...])
```
**app.use()** takes an **optional path parameter** as its first argument. We can now write middleware that will run for every request at a specific path.
```js
app.use('/sorcerer', (req, res, next) => {
  console.log('User has hit endpoint /sorcerer');
  next();
});
```
In the example above the console will print 'User has hit endpoint /sorcerer', if someone visits our web page's '/sorcerer' endpoint. Since the method app.use() was used, it won't matter if the user is performing a GET,a POST, or any other kind of HTTP request. 

The request **req** and response **res** objects are passed forward when using **next()**.


### Control Flow With next()
We don't always want to pass control to the next middleware in the stack.
For example, when designing a system with confidential information, we want to be able to selectively show that information to authorized users.
We would create middleware that tests a user's permissions. If the user has the permission necessary, we would continue through the middleware stack by calling next(). If it fails, we would want to let the user know that they're not allowed to see the information they're trying to access.

## Route-Level app.use() - Multiple Path
The **PATH argument** of **app.use()**:
The path for which the middleware function is invoked; can be any of:
-A string representing a path.
-A path pattern.
-A regular expression pattern to match paths.
-**An array of (paths)** combinations of any of the above. 

```js
app.use(['/beans/', '/beans/:beanName'], (req, res, next) => {      //Used for two paths
  let bodyData = '';
  req.on('data', (data) => { bodyData += data; }); //The body of the request has been received
  req.on('end', () => {       //Full request has been received
    if (bodyData) { req.body = JSON.parse(bodyData); }
    next();
  });
});
```

### Using named functions for the Middleware Stacks
```js
const logging = (req, res, next) => {
  console.log(req);
  next();
};

app.use(logging);
```

### Multiple callbacks as additional parameters for GET POST PUT..
```js
const authenticate = (req, res, next) => {
  ...
};

const validateData = (req, res, next) => {
  ...
};

const getSpell = (req, res, next) => {
  res.status(200).send(getSpellById(req.params.id));
};

const createSpell = (req, res, next) => {
  createSpellFromRequest(req);
  res.status(201).send();
};

const updateSpell = (req, res, next) => {
  updateSpellFromRequest(req);
  res.status(204).send();
}

app.get('/spells/:id', authenticate, getSpell);

app.post('/spells', authenticate, validateData, createSpell);

app.put('/spells/:id', authenticate, validateData, updateSpell);
```
In the above code sample, we created **reusable middleware** for authentication and data validation. We use the authenticate() middleware to verify a user is logged in before proceeding with the request and we use the validateData() middleware before performing the appropriate create or update function. **Additional middleware can be placed at any point in this chain**.

## Express Middleware resources https://expressjs.com/en/resources/middleware.html

## Open-Source Middleware: Logging with "Morgan"
Morgan - an open-source library for logging information about the HTTP request-response cycle in a server application.

**morgan()** is a function that **will return a middleware function**, to reiterate: the **return value of morgan() will be a function**, that function will have the **function signature (req, res, next)** that **can be inserted into an app.use()**, and that function will be called before all following middleware functions.

Morgan takes an argument to describe the formatting of the logging output. We will be using **morgan('tiny')**.
Options and other documentation: https://github.com/expressjs/morgan

## Open-Source Middleware: Parsing the body with "bodyParser"
Node.js body parsing middleware.
Parse incoming request bodies in a middleware before your handlers, available under the req.body property.

## Error handling middleware
Error handling middleware needs to be the last app.use() in your file. If an error happens in any of our routes, we want to make sure it gets passed to our error handler. The middleware stack progresses through routes as they are presented in a file, therefore the error handler should sit at the bottom of the file. 
```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```
If we anticipate an operation might fail, we can invoke our error-handling middleware. We do this by passing an error object as an argument to next().
```js
app.use((req, res, next) => {
  const newValue = possiblyProblematicOperation();
  if (newValue === undefined) {
    let undefinedError = new Error('newValue was not defined!');
    return next(undefinedError);
  }
  next();
});

app.use((err, req, res, next) => {
  const status = err.status || 500;
  res.status(status).send(err.message);
});

```

# Router Parameters
### router.param()
When a specific parameter is present in a route, we can write a function that will perform the necessary lookup and attach it to the req object in subsequent middleware that is run.
```js
app.param('spellId', (req, res, next, id) => {
  SpellBook.find(id, (err, spell) => {
    if (err) { next(err); } 
    else if (spell) { req.spell = spell; next(); } 
    else { next(new Error('Your magic spell was not found in any of our tomes')); }
  });
});
```
In the code above we **intercept any request to a route handler with the :spellId parameter**. Note that in the app.param function signature, **'spellId' does not have the leading :**. The actual ID will be passed in as **the fourth argument, id in this case**, to the **app.param callback function** when a request arrives.
We look up the spell in our SpellBook array using the .find() method. If SpellBook does not exist, or some other error is thrown in this process, we pass the error to the following middleware (hopefully we've written some error-handling middleware, or included some externally-sourced error-handling middleware). If the spell exists in SpellBook, we **attach it as a property of the request object** (so future routes can reference it via **req.spell**). If the requested spell does not exist, we let future middleware know there was an error with the request by creating a new Error object and passing it to next().
Note that inside an app.param callback, you should use the fourth argument as the parameter's value, not a key from the req.params object.

## Merge parameters - nested routers
When we're building web endpoints, we might want to access some property of a complex object. In order to do this in Express, we can **design a nested router**. This would be a router that is **invoked within another router**. In order to **pass parameters the parent router has access to**, we pass a special configuration object when defining the router.
```js
const sorcererRouter = express.Router();
const familiarRouter = express.Router({mergeParams: true});

sorcererRouter.use('/:sorcererId/familiars', familiarRouter);

sorcererRouter.get('/', (req, res, next) => {
  res.status(200).send(Sorcerers);
  next();
});

sorcererRouter.param('sorcererId', (req, res, next, id) => {
  const sorcerer = getSorcererById(id);   
  req.sorcerer = sorcerer;
  next();
});

familiarRouter.get('/', (req, res, next) => {
  res.status(200).send(`Sorcerer ${req.sorcerer} has familiars ${getFamiliars(sorcerer)}`);
});

app.use('/sorcerer', sorcererRouter);
```
In the code above we define two endpoints: **/sorcerer** and **/sorcerer/:sorcererId/familiars**. The **familiars are nested into the sorcerer endpoint** — indicating the relationship that a sorcerer has multiple familiars. Take careful note of the {mergeParameters: true} argument that gets passed when creating the familiarRouter. This argument tells Express that the familiarRouter should have access to parents passed into its parent router, that is, the sorcererRouter. We then tell express that the path for the familiarRouter is the same as the path for the sorcererRouter with the additional path /:sorcererId/familiars. We then can create a family of routes (a router) built by appending routes to familiarRouter's base: /sorcerer/:sorcererId/familiars.


