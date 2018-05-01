## 1. HELLO WORLD!

Create an Express.js app that outputs "Hello World!" when somebody goes to /home.

The port number will be provided to you by expressworks as the first argument of
the application, ie. process.argv[2].

```javascript
var express = require('express');

var app = express();
app.get('/home', function(req, res){
    res.end("Hello World!");
});

app.listen(process.argv[2]);
```

## 2. STATIC

Apply static middleware to serve index.html file without any routes.

Your solution must listen on the port number supplied by process.argv[2].

The index.html file is provided and usable via the path supplied by
process.argv[3]. However, you can use your own file with this content:

```javascript
    <html>
      <head>
        <title>expressworks</title>
        <link rel="stylesheet" type="text/css" href="/main.css"/>
      </head>
      <body>
        <p>I am red!</p>
      </body>
    </html>
```
### HINTS

This is how you can call static middleware:

    app.use(express.static(path.join(__dirname, 'public')));

For this exercise expressworks will pass you the path:

    app.use(express.static(process.argv[3]||path.join(__dirname, 'public')));

```javascript
var express = require('express');
var path = require('path');

var app = express();
  app.use(express.static(process.argv[3]||path.join(__dirname, 'public')));
  //app.use(express.static(process.argv[3])); also works

app.listen(process.argv[2]);
```

## 3. JADE/PUG tamplate enging

Create an Express.js app with a home page rendered by Jade template engine.

The homepage should respond to /home.

The view should show the current date using toDateString.

### HINTS

The Jade template file index.jade is already provided:

    h1 Hello World
    p Today is #{date}.

This is how to specify path in a typical Express.js app when the folder is
'templates':

    app.set('views', path.join(__dirname, 'templates'))

However, to use our index.jade, the path to index.jade will be provided as
process.argv[3].  You are welcome to use your own jade file!

To tell Express.js app what template engine to use, apply this line to the
Express.js configuration:

    app.set('view engine', 'jade')

Instead of Hello World's res.end(), the res.render() function accepts
a template name and presenter data:

    res.render('index', {date: new Date().toDateString()})

We use toDateString() to simply return the date in a human-readable format
without the time.

```javascript
var express = require("express");
var app = express();
var path = require('path');

app.set('views', path.join(__dirname, 'templates'));
//app.set('views', __dirname + '/templates');
app.set("view engine", "pug");

app.get('/home', function(req, res){
    res.render('index', {date: new Date().toDateString()});
});

app.listen(process.argv[2]);
```
## 4. FORM (POST)

Write a route ('/form') that processes HTML form input
(<form><input name="str"/></form>) and prints backwards the str value.

### HINTS

To handle POST request use the post() method which is used the same way as get():

    app.post('/path', function(req, res){...})

Express.js uses middleware to provide extra functionality to your web server.

Simply put, a middleware is a function invoked by Express.js before your own
request handler.

Middlewares provide a large variety of functionalities such as logging, serving
static files and error handling.

A middleware is added by calling use() on the application and passing the
middleware as a parameter.

To parse x-www-form-urlencoded request bodies Express.js can use urlencoded()
middleware from the body-parser module.

    var bodyparser = require('body-parser')
    app.use(bodyparser.urlencoded({extended: false}))

Read more about Connect middleware here:

  [https://github.com/senchalabs/connect#middleware](https://github.com/senchalabs/connect#middleware)

The documentation of the body-parser module can be found here:

  [https://github.com/expressjs/body-parser](https://github.com/expressjs/body-parser)

Here is how we can flip the characters:

    req.body.str.split('').reverse().join('')

### NOTE

When creating your projects from scratch, install the body-parser dependency
with npm by running:

    $ npm install body-parser

…in your terminal.

```javascript
var express = require("express");
var bodyParser = require('body-parser');

var app = express();

app.use(bodyParser.urlencoded({ extended: false }));

app.post('/form', function(req, res){
  res.end(req.body.str.split('').reverse().join(''));
});

app.listen(process.argv[2]);
```

## 5. STYLISH CSS: STYLUS

Style your HTML from previous example with some Stylus middleware.

Your solution must listen on the port number supplied by process.argv[2].

The path containing the HTML and Stylus files is provided in process.argv[3]
(they are in the same directory). You can create your own folder and use these:

The main.styl file:

    p
      color red

The index.html file:

    <html>
      <head>
        <title>expressworks</title>
        <link rel="stylesheet" type="text/css" href="/main.css"/>
      </head>
      <body>
        <p>I am red!</p>
      </body>
    </html>

### HINTS

To plug-in stylus someone can use this middleware:

    app.use(require('stylus').middleware(__dirname + '/public'));

Remember that you need also to serve static files.

    npm install stylus

```javascript
var express = require("express");
var stylus = require('stylus');
var app = express();


app.use(stylus.middleware(__dirname + '/public'));
app.use(express.static(process.argv[3])); //it worked with (__dirname + '/public') why?
//returns style document

app.listen(process.argv[2]);
```

## 6. PUT HASH

```javascript
var express = require("express");
var crypto = require("crypto");

var app = express();

app.put('/message/:id', function(req, res){
    
    var result = crypto.createHash('sha1')
      .update(new Date().toDateString() + req.params.id)
      .digest('hex');
    res.send(result);
});

app.listen(process.argv[2]);
```

## 7. QUERY TO JSON

```javascript
var express = require("express");

var app = express();

app.get('/search', function(req, res){
    var obj = req.query;
    res.send(obj);
});

app.listen(process.argv[2]);
``` 

## 8. FILE TO JSON

```javascript
var express = require("express");
var fs = require('fs');

var app = express();
 
var resultObj;
function fileToJson(err, data){
  if (err){
    console.log("error");
  } else {
    resultObj = JSON.parse(data);
    //console.log(resultObj);
  }
}

fs.readFile(process.argv[3], fileToJson);
app.get('/books', function(req, res){
    res.json(resultObj);
});

app.listen(process.argv[2]);

/* official solution
var express = require('express')
    var app = express()
    var fs = require('fs')
    
    app.get('/books', function(req, res){
      var filename = process.argv[3]
      fs.readFile(filename, function(e, data) {
        if (e) return res.send(500)
        try {
          books = JSON.parse(data)
        } catch (e) {
          res.send(500)
        }
        res.json(books)
      })
    })
    
    app.listen(process.argv[2])*/
```

