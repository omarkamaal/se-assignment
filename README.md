# Assignment 1

## Objective

Demonstrate your mastery of:

- running nodejs
- setting up a mongodb database
- useing express
- connecting to the database
- responding with a file
- responding with JSON
- node module syntax (require/export) using the module design pattern
- npm
- testing your code
    - unit
    - end to end
- some html
- some css
- jquery or just javacript in the browser
- ajax


## Requires

- Internet
- Google chrome
- A plain text editor (preferably sublime)
- mongodb
- nodejs
- git
- A github account

## Motivation

We feel SE students will go through harsh trials in the near future, accordingly we thought of having you build an inspirational quote app. May it lift your spirit in your time of need.

## Task

Your job is to create a simple test driven inspirational quotes application that will show you a new inspirational quote every-time you click on the page as well as the author of the quote in the bottom right, the quote should be loaded using ajax.

The application should inform the user somehow on how he can use it, in my case I made it so that the first time the app loads starts it says on the screen that you should click/tap



> If you want to have fun you can modify the data we give you and create your own set of quotes - for example make it an Arabic movie quotes app.

### UI

![Image of application when it starts](assets/inspire-me-start.png)

![Image of application after one click](assets/inspiration.png)

### File structure

You are to fork the assignment repo and add your own folder with your id.
You should windup with the following structure.

```
|- cs-16-5240/
|  |- public/
|  |  |- index.html 
|  |
|  |- test/
|  |  |- quote.js
|  |
|  |- server.js
|  |- quotes.js
|  |- db.js            <------ this is partially provided bellow
|  |- package.json
|  
|- quotes.json
|- .gitignore
```

You will find in this repo the `.gitignore` and `quotes.json` files everything else you have to create yourself.

- `public/index.html` Should contain the html as well as the styles and javascript need to run the app.
- `server.js` contains code that runs the server and handles requests.
- `/quotes.js` should contain the functionality related to quotes
- `test/quotes.js` should contain the tests that make sure all functionality in the quotes.js file work.
- `db.js` In my example this is a file that hold functions for connecting and accessing the database.
- `package.json` should contain all your dependencies in addition to your npm scripts; start, test, and coverage.

### Features

- The app must serve the data from a mongodb database.
- The app should implement a simple GET API for getting quotes.

#### quotes.js

Quotes.js module should export the following functions

##### `getElementByIndexElseRandom(array [, index])` 

- Given an array returns:
    - A random element from the array If index is not passed.
    - The element in the correct index position if it is.

```js
getElementByIndexElseRandom([1, 3, 4])    // any of 1 3 or 4
getElementByIndexElseRandom([1, 3, 4], 0) // always 1
```


##### `getQuotesFromJSON()`

- Will call the callback function passed __cb__ with arguments `error, quotes`
    - error will be null if no error occurred
    - quotes is a list of all quotes
    
```js
getQuotesFromDB(function (err, quotes) {
    // any of quote object in the database  
})
```

##### `getQuoteFromJSON(index)`

- returns a random quote from the quotes.json file if index is not passed else the on int the index position.

```js
getQuoteFromJSON()           // any of quote object in the quotes.json file
getQuoteFromJSON(0).author   // Kevin Kruse
```

##### `seed(cb)`

Populate the database with quotes from quotes.json, seed should call the call back when done with an `error, seeded` set of arguments.

seeded is a boolean value that is true if the database was empty (and thus seeded) or no error occurred but the database already contains records.

```js

```


##### `getQuotesFromDB(cb)`

- Will call the callback function passed __cb__ with arguments `error, quotes`
    - error will be null if no error occured
    - quotes is a list of all quotes
    
```js
getQuotesFromDB(function (err, quotes) {
    // any of quote object in the database  
})
```

##### `getQuoteFromDB(cb [, index])`

- Will call the callback function passed __cb__ with arguments `error, quote`
    - error will be null if no error occured
    - quote should contain a random quote document returned from the database
- Optional arguemtn index if present will select a sepcific quote by index from the quotes documents returned.

```js
getQuoteFromDB(function (err, quote) {
    // any of quote object in the database  
})
getQuoteFromDB(function (err, quote) {
    // is Kevin Kruse assuming it's the first document in the database
    quote.author;  
}, 0)
```


#### db.js

##### `connect(cb)`
Connects to database then call callback passing db

##### `clearDb(cb)`
clears the database used largely in testing. then call callback

#### API

The server needs to serve index.html when we visit `/index.html`, `index`, or just `/`. Any other url not supported should return a 404 not found.

The following API route endpoint should exist

`/quote` returns a JSON response that was randomly selected from the database
`/quotes` returns a JSON array as response containing all quotes in the database.

- When a user clicks on the page on index.html; a script should send a `GET` request that follows the API description, returning a random quote form the database as JSON, which in turn is then used to update the html page.

You need will to write tests as well as run a coverage test suit

### Suggestions

You will need to add your dependencies in package.json

for this assignemnt you will as a minimum use `mocha`, `instanbule` and `mongodb` packages

I recommend `chai` for behavioral testing

> The quote test should test all functions in quote.js

to test the random selection you can assert that the returned quote is one of the quotes you can get from quotes.json

you can read a json file using require `require('quotes.js')`


you will need to clear the database before you run tests

you can do this by calling `before(db.clearDB)` assuming you have the following


```js
// db.js

var mongo = require('mongodb').MongoClient;
var DB = null;
var dbURL = 'mongodb://localhost:27017/quotes';

exports.connect = function(cb) {
    if (DB) {
        return cb(DB);
    }
    return mongo.connect(dbURL).then(function(db) {
        console.log('connected to db');
        DB = db;
        if (cb) cb(null, db);
        else return db;
    });
};

exports.db = function() {
    if (DB === null) throw Error('DB Object has not yet been initialized');
    return DB;
};


exports.clearDB = function(done) {
    DB.listCollections().toArray().then(function (collections) {
        collections.forEach(function (c) {
            DB.collection(c.name).removeMany();   
        });
        done();
    }).catch(done);
};

```

```js
// tests/quotes.js

var assert = require('chai').assert;
var quotes = require('../quotes.js');
var db = require('../db.js');
before(function (done) {
    db.connect(function (db) {
        done();
    }).catch(done);
});


describe('Quotes DB', function () {
    before(db.clearDB);
    //....
});
describe('Quotes JSON', function () {
    // ...
});

```

