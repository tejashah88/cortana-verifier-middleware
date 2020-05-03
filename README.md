# cortana-verifier-middleware

[![NPM Version](https://img.shields.io/npm/v/cortana-verifier-middleware.svg)](https://www.npmjs.com/package/cortana-verifier-middleware/)
[![Build Status](https://travis-ci.org/tejashah88/cortana-verifier-middleware.svg?branch=master)](https://travis-ci.org/tejashah88/cortana-verifier-middleware)
[![Coverage Status](https://coveralls.io/repos/github/tejashah88/cortana-verifier-middleware/badge.svg)](https://coveralls.io/github/tejashah88/cortana-verifier-middleware)
[![dependencies Status](https://david-dm.org/tejashah88/cortana-verifier-middleware/status.svg)](https://david-dm.org/tejashah88/cortana-verifier-middleware)

An [express](https://www.npmjs.com/package/express) middleware that verifies HTTP requests sent to a Cortana skill are sent from Microsoft.

### Usage

It is recommended that you attach all Cortana routes to an express Router.
```javascript
var express  = require('express');
var verifier = require('cortana-verifier-middleware');

var app = express();

// create a router and attach to express before doing anything else
var cortanaRouter = express.Router();
app.use('/cortana', cortanaRouter);

// attach the verifier middleware first because it needs the entire
// request body, and express doesn't expose this on the request object
cortanaRouter.use(verifier);

// Routes that handle cortana traffic are now attached here.
// Since this is attached to a router mounted at /cortana,
// this endpoint will be accessible at /cortana/weather_info
cortanaRouter.get('/weather_info', function(req, res) { ... });

app.listen(3000);
```

### Common errors

#### The raw request body has already been parsed.
* This means that you're probably using one of the body-parser middlewares and it is loaded before this one. To fix it, you should load the body-parsers **after** this one.

Before:
```javascript
var cortanaRouter = express.Router();
app.use('/cortana', cortanaRouter);

// INCORRECT
cortanaRouter.use(bodyParser.json());
cortanaRouter.use(verifier);
```

After:
```javascript
var cortanaRouter = express.Router();
app.use('/cortana', cortanaRouter);

// CORRECT
cortanaRouter.use(verifier);
cortanaRouter.use(bodyParser.json());
```

### Mentions
* [mreinstein](https://github.com/mreinstein) for his [cortana-verifier](https://github.com/mreinstein/cortana-verifier) module, which allows you to verify any Microsoft requests from any web service