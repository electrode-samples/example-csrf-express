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



// csrf-jwt plugin configuration, should come after cookieParser() and before routes `app.use('/', index);`
const csrfOptions = {
  secret: "test",
  expiresIn: 60
};

app.use(csrfMiddleware(csrfOptions));

```

## Test
* CSRF Protection demo
* Let's add some code to verify CSRF
* Add the file `app/public/javascripts/csrf.js`:

```js
console.log("working");

function doPOST(csrfHeader, shouldFail, resultId) {
  $.ajax({
    type: 'POST',
    data: JSON.stringify({message: "hello"}),
    headers: {
      "x-csrf-jwt": csrfHeader
    },
    xhrFields: {
      withCredentials: true
    },
    contentType: 'application/json',
    url: '/2',
    success: function(data, textStatus, xhr) {
      var msg = 'POST SUCCEEDED with status ' + xhr.status + ' ' + (shouldFail ? 'but expected error' : 'as expected');
      console.log(msg);
      $(resultId).html('<p>' + msg + '</p>');
    },
    error: function(xhr, textStatus, error) {
      var msg = 'POST FAILED with status ' + xhr.status + ' ' + (shouldFail ? 'as expected' : 'but expected success');
      console.log(msg);
      $(resultId).html('<p>' + msg + '</p>');
    }
  });
}


$(function() {
  $('#test-valid-link').click(function(e) {
    e.preventDefault();
    console.log('test-valid-link clicked');
    $.ajax({
      type: 'GET',
      url: '/1',
      xhrFields: {
        withCredentials: true
      },
      success: function(data, textStatus, xhr) {
        console.log('GET: success');
        var csrfHeader = xhr.getResponseHeader('x-csrf-jwt');
        if (csrfHeader != '') {
          console.log('> Got x-csrf-jwt token OK\n');
        }
        var csrfCookie = Cookies.get('x-csrf-jwt');
        if (csrfCookie != '') {
          console.log('> Got x-csrf-jwt cookie OK\n');
        }

        doPOST(csrfHeader, false, '#test-results');
      }
    });
  });

  $('#test-invalid-link').click(function(e) {
    e.preventDefault();
    console.log('test-invalid-link clicked');
    doPOST('fake', true, '#test-results');
  });

});
```

* Add the file `app/views/csrf.jade`:

```
extends layout

block content
  script(type='text/javascript' src='http://code.jquery.com/jquery-3.1.0.min.js')
  script(type='text/javascript' src='https://cdnjs.cloudflare.com/ajax/libs/js-cookie/2.1.3/js.cookie.js')
  script(type='text/javascript' src='/javascripts/csrf.js')
  h1= title
  p This page demonstrates usage of the #[a(href="https://github.com/electrode-io/electrode-csrf-jwt") electrode-csrf-jwt] module. Two endpoints are declared in #[code app.js]:
    ul
      li a GET endpoint, #[code /1], to which the module automatically adds a csrf token header
      li a POST endpoint, #[code /2], to which the module automatically ensures the presence of a valid token in the request headers
  p Two simple tests via AJAX (JavaScript must be enabled) are demonstrated below:
    ul
      li #[a#test-valid-link(href="#") Test Valid POST] using a token retrieved from #[code /1] first (should succeed with status 200)
      li #[a#test-invalid-link(href="#") Test Invalid POST] using a forged token (should fail with status 500)
  #test-results
```

* Update `app/routes/index.js` with the following routes:

```js
/* GET CSRF demo page. */
router.get('/csrf', function(req, res, next) {
  res.render('csrf', { title: 'CSRF Protection Demo' });
});

/* GET demo endpoint which will return a CSRF cookie + header */
router.get('/1', function(req, res, next) {
  res.end("valid");
});

/* POST demo endpoint which will require a CSRF cookie + header */
router.post('/2', function(req, res, next) {
  return res.end("valid");
});
```

## Run
* Start express app:

```bash
npm start
```

* Navigate to `http://localhost:3000/csrf` to test the CSRF features

[electrode-csrf-jwt]: https://github.com/electrode-io/electrode-csrf-jwt