# Express-Kun

Express Kun is providing you common helper for common express use case with functional programming mindset.

## Philosophy

An helper should only extends the functionality without redefining how we write our application.
think of it as Redux vs Mobx. in Redux, you don't redefine how you write your components. redux only extends the functionality fia `connect` function to access your reducer. meanwhile MobX requiring you to wrap your function in decorator. by extending instead of redefining we can achieve smoother learning curve and more expressive.
Express Kun does not dictate you. instead, you provided it with `express` `Router` object and returning modified `Router` express object or providing callback to play with modified `Router` Object.
Express Kun is your friend and servant.

## API

### function withMiddleware(router: Router, middlewares: RequestHandler[]): Router;

with middleware apply middleware to your router and return back the midlewared router

```javascript
// your router
const router = Router();
// with auth middleware
const protectedRouter = withMiddleware(router, authMiddleware); // also support array of middleware ex: [authMiddleware, myMiddleware2]

protectedRouter.get("/user", (req, res) => {
  res.send({
    message: "success"
  });
});
```

because this is only return the midlewared router. you can chain it without modifying the first router behavior

```javascript
// your router
const router = Router();
// with auth middleware
const protectedRouter = withMiddleware(router, authMiddleware); // also support array of middleware ex: [authMiddleware, myMiddleware2]
// will apply authMiddleware and uploadMiddleware
const protectedUploadRouter = withMiddleware(protectedRouter, uploadMiddleware);

protectedRouter.get("/user", (req, res) => {
  res.send({
    message: "success"
  });
});
protectedUploadRouter.post("/user", (req, res) => {
  res.send({
    message: "success upload photo"
  });
}))
```

### `groupErrorHandler(routerOrApp: RouterOrApplication, handler: ErrorRequestHandler): (callback: (router: Router) => void) => void;`

this wass Laravel-like route for reusable error handling. you can write it like this

```javascript
// your router
const router = Router();
// define error handler
const errorHandler = (err, req, res, next) => {
  // log the error
  console.log(error);

  // sendJson
  res.json({
    error: true,
    messsage: "wow error"
  });
};

// use it
groupErrorHandler(
  router,
  errorHandler
)(withErrorHandlerRoute => {
  // when every route defined here return an error it will passed to the error handler
  withErrorHandleRoute.get("/testError", (req, res) => {
    throw new Error("error Here");
  });
});
```

by providing error this way you can provide multiple error handle for different route easily

```javascript
const userRouter = Router();
const customerRouter = Router();
// define error handler
const errorHandlerUser = (err, req, res, next) => {
  // log the error
  console.log(error);

  // sendJson
  res.json({
    error: true,
    messsage: "wow error in user"
  });
};

const errorHandlerCustomer = (err, req, res, next) => {
  // log the error
  console.log(error);

  // sendJson
  res.json({
    error: true,
    messsage: "wow error in customer"
  });
};

// use it
groupErrorHandler(
  userRouter,
  errorHandlerUser
)(withErrorHandlerRoute => {
  // when every route defined here return an error it will passed to the error handler
  withErrorHandleRoute.get("/user/testError", (req, res) => {
    throw new Error("error Here");
  });
});

groupErrorHandler(
  customerRouter,
  errorHandlerCustomer
)(withErrorHandlerRoute => {
  // when every route defined here return an error it will passed to the error handler
  withErrorHandleRoute.get("/customer/testError", (req, res) => {
    throw new Error("error Here");
  });
});
```

### `groupMiddleware(router: Router, middlewares: SupportedMiddleware): (callback: (router: Router) => void) => void;`

This is like `groupErrorHandler` but instead of error handler you can pass middleware here
for example

```javascript
// your router
const router = Router();
// define error handler
const myMiddleware = (err, req, res, next) => {
  // log or do something
  console.log("logging in something");
};

// use it
groupMiddleware(
  router,
  myMiddleware // also support array of middleware ex: [myMiddleware, myMiddleware2]
)(middlewaredRoute => {
  // when every route defined here return an error it will passed to the error handler
  middlewaredRoute.get("/user/middlewared", (req, res) => {
    res.send("hello world");
  });
});
```

### `groupPrefix(router: Router, prefix: string): (callback: (router: Router) => void) => void;`

Provide laravel-like grouping route/controller with the same prefix

```javascript
// your router
const router = Router();
// add prefix
groupPrefix(
  router,
  "/customer"
)(prefixedRoute => {
  // the route will be '/customer/test'
  prefixedRoute.get("/test", (req, res) => {
    res.send("hello world");
  });
});
```