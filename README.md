# example-csrf-express
* This repo is an example express.js app with [electrode-csrf-jwt] module fully integrated
* The step-by-step instructions on building it from scratch can be found below

## Electrode CSRF-JWT
[electrode-csrf-jwt] is an Electrode plugin that allows you to authenticate HTTP requests using JWT in your Electrode applications.

## yeoman + express-generator
* This app was scaffolded using [yeoman] and [express-generator]
* First, install `yeoman` and `express-generator`:

```bash
npm install -g yo
npm install -g express-generator
```

* Scaffold a new app using `yeoman`:

```bash
express expressApp
cd expressApp
npm install
```

* At this point, you should be able to run the server locally:

```bash
NODE_ENV=development npm start
```

## Electrode CSRF-JWT

### Install
* Add the `electrode-csrf-jwt` component:

```bash
npm install electrode-csrf-jwt --save
```

* Next, in `expressApp/app.js`, register the component with the Express app:

```javascript
const csrfMiddleware = require("electrode-csrf-jwt").expressMiddleware;

var app = express();

const csrfOptions = {
  secret: "test",
  expiresIn: 60
};

app.use(csrfMiddleware(csrfOptions));

```

That's it! CSRF protection will be automatically enabled for endpoints added to the app. CSRF JWT tokens will be returned in the headers and set as cookies for every response and must be provided as both a header and a cookie in every `POST` request.

> You can read more about options and usage details on [the component's README page](https://github.com/electrode-io/electrode-csrf-jwt#usage)

In addition to the above steps, the following modifications were made in order to demonstrate functionality:

* Two endpoints were added to `app/routes/index.js`
* AJAX testing logic was added in `app/public/javascripts/csrf.js`
* Links to tests were added to `app/views/csrf.jade`

[electrode-csrf-jwt]: https://github.com/electrode-io/electrode-csrf-jwt