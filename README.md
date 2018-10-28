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
