# jupiter-router

[![Build Status](https://travis-ci.org/Jupiter-framework/jupiter-router.svg)](https://travis-ci.org/Jupiter-framework/jupiter-router)
[![Coverage Status](https://coveralls.io/repos/Jupiter-framework/jupiter-router/badge.svg?branch=master&service=github)](https://coveralls.io/github/Jupiter-framework/jupiter-router?branch=master)

Simple declarative router

* [Using middleware in `jupiter-router`](#middleware)
* [API](#api)
  * [Router](#router)
    * [.handle](#handle)
    * [.sub](#sub-in-router)
  * [Route](#route)
    * [.sub](#sub-in-route)
  * [Req](#req)
  * [Helpers](#helpers)
    * [HTTP helpers](#http-helpers)
    * [Response helpers](#http-helpers)


## Middleware

Example of using `express.js` middleware in `jupiter-router` on
[body-parser](https://github.com/expressjs/body-parser) middleware.

```javascript
import bodyParser from 'body-parser';
import { Router, Route, wrap } from 'jupiter-router';

Router.sub(
  Route('/', wrap(bodyParser));
)

```

## API

### Router

Create instance of Router

**Example:**

```javascript
import { createServer } from 'http';
import { Router } from 'jupiter-router';

const router = Router();

createServer((req, res)=> router.handle(req, res)).listen(3000);
```

#### .handle

Wrap and handle all requests into server

**Arguments:**
* req - { http.IncomingMessage } Native `IncomingMessage` instance
* res - { http.ServerResponse } Native `ServerResponse` instance

#### .sub (in router)

Bind routes for handling in router. Behavoir is alias of
[`.sub` of route](#sub-in-route)

### Route

**Example:**

```javascript
import { createServer } from 'http';
import { Router, Route, json } from 'jupiter-router';

const router = Router();

router.sub(
  Route('/', get((req)=> Promise.resolve(json({hello: 'world'}))))
);

createServer((req, res)=> router.handle(req, res)).listen(3000);

// localhost:300 return application/json { hello: 'world' }
```

#### .sub (in route)

Bind handler and subroutes for handler requests in this route.

**Arguments:**
* routePath - { String } Path of binding route. Can be use parametrized,
using `:param` notation.
* handler - { Function } **optional** handler of this route. Can be wrapped in
`get()`, `post()`, `put()`, `del()` helper functions. May be return simple
value or Promise. Result of handler can be wrapped in `html()`, `json()`
helper.

**Example:**

```javascript
import { createServer } from 'http';
import { Router, Route, json } from 'jupiter-router';

const router = Router();

// simple route
router.sub(
  Route('/', get((req)=> Promise.resolve(json({hello: 'world'}))))
);

// nested routes
router.sub(
  Route('/', someFuncHandler).sub(
    Route('/users' someUsersFuncHandler),
    Route('/user/:id' someUsersFuncHandler),
  )
);

// RESTfull routes
router.sub(
  Route('/').sub(
    Route('/user' get(getListUser)),
    Route('/user/:id' get(getOneUser)),
    Route('/user' post(createUser)),
    Route('/user/:id' put(updateUser)),
    Route('/user/:id' patch(updateUserWithDiff)),
    Route('/user/:id' del(removeUser)),
  )
);

createServer((req, res)=> router.handle(req, res)).listen(3000);
```

### Req

### Res

### Helpers

#### Middleware helpers

* `wrap(middleware)` - Wrap of middleware and redefine last argument. Return
function

#### HTTP helpers

Wrap handler for executing on custom HTTP method

* `get(handler)` - Bind handler on `GET` method
* `post(handler)` -  Bind handler on `POST` method
* `put(handler)` -  Bind handler on `PUT` method
* `patch(handler)` -  Bind handler on `PATCH` method
* `delete(handler)` -  Bind handler on `DELETE` method

#### Response method

Wrap result of handlers for correct response format and headers.

* `html(result)` - return `result` with `text/html` content type
* `json(result)` - return `result` with `application/json` content type
* `redirect(redirectPath)` - redirect request to `redirectPath`
