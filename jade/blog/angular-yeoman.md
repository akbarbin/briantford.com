# Using Yeoman with AngularJS

<span class="date">[2012.09.10]</span>

[Yeoman](http://yeoman.io/) is a new tool for simplifying your client-side development workflow. It supports a variety of different tools and frameworks, but I'm going to focus specifically on how it helps write AngularJS applications.

Out of the box, Yeoman includes some excellent features for building AngularJS apps. The best of these features is its generators. Generators allow you to quickly create new controllers, directives, services, unit tests, and even entire applications. It gives you the speed of boilerplates/seeds but with added flexibility. Let's look at an example. Yeoman infers the name of your project based on the name of its parent directory, so let's make a new directory and try it out:

```shell
λ → mkdir yeoman-test
λ → cd yeoman-test
λ → yeoman init angular
```

Yeoman will report creating the following files:

```shell
.. Invoke angular ..

.. Invoke angular:app:angular ..
Writing app/.htaccess...OK
Writing app/404.html...OK
Writing app/favicon.ico...OK
Writing app/robots.txt...OK
Writing app/scripts/vendor/angular.js...OK
Writing app/scripts/vendor/angular.min.js...OK
Writing app/styles/main.css...OK
Writing Gruntfile.js...OK
Writing package.json...OK
Writing test/lib/angular-mocks.js...OK
Writing app/scripts/yeoman-test.js...OK
Writing app/index.html...OK
Writing app/scripts/controllers/main.js...OK
Writing app/views/main.html...OK

.. Invoke testacular:app:angular ..
Writing testacular.conf.js...OK
```

If you're familiar with the [AngularJS seed](https://github.com/angular/angular-seed), this project structure should look very familiar.

We can easily serve our front end with:

```shell
λ → yeoman server
```

Which automatically opens your web browser, and will refresh your browser if you make changes to your application.

As you've probably noticed, the default generated app isn't very interesting, but let's look at the code. First, `app/index.html`

```html
<!DOCTYPE html>
<!--[if lt IE 7]>      <html class="no-js lt-ie9 lt-ie8 lt-ie7"> <![endif]-->
<!--[if IE 7]>         <html class="no-js lt-ie9 lt-ie8"> <![endif]-->
<!--[if IE 8]>         <html class="no-js lt-ie9"> <![endif]-->
<!--[if gt IE 8]><!--> <html class="no-js"> <!--<![endif]-->
    <head>
        <meta charset="utf-8"/>
        <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"/>
        <title></title>
        <meta name="description" content=""/>
        <meta name="viewport" content="width=device-width"/>
        <link rel="stylesheet" href="styles/main.css"/>
    </head>
    <body ng-app="yeomanTestApp">

        <!--[if lt IE 7]>
            <p class="chromeframe">You are using an outdated browser. <a href="http://browsehappy.com/">Upgrade your browser today</a> or <a href="http://www.google.com/chromeframe/?redirect=true">install Google Chrome Frame</a> to better experience this site.</p>
        <![endif]-->

        <!-- Add your site or application content here -->
        <div class="container" ng-view></div>

        <script src="scripts/vendor/angular.js"></script>

        <!-- build:js scripts/scripts.js -->
        <script src="scripts/yeoman-test.js"></script>
        <script src="scripts/controllers/main.js"></script>
        <!-- endbuild -->

        <!-- Google Analytics: change UA-XXXXX-X to be your site's ID. -->
        <script>
            var _gaq=[['_setAccount','UA-XXXXX-X'],['_trackPageview']];
            (function(d,t){var g=d.createElement(t),s=d.getElementsByTagName(t)[0];
            g.src=('https:'==location.protocol?'//ssl':'//www')+'.google-analytics.com/ga.js';
            s.parentNode.insertBefore(g,s)}(document,'script'));
        </script>
    </body>
</html>
```

Based on the HTML5 Boilerplate, this index file also includes an `ngView` and the script files needed by our application. Notice that the script file is named `yeoman-test.js`. This is because, as mentioned before, Yeoman infers the name of your project based on the name of its parent directory.

Let's take a look at `yeoman-test.js`:

```javascript
'use strict';

var yeomanTestApp = angular.module('yeomanTestApp', [])
  .config(['$routeProvider', function($routeProvider) {
    $routeProvider
      .when('/', {
        templateUrl: 'views/main.html',
        controller: 'MainCtrl'
      })
      .otherwise({
        redirectTo: '/'
      });
  }]);
```

This creates our application-level module, then provides some basic routing. Notice that there is one view defined: `views/main.html`. For the sake of completion, let's take a look at this view, and its corresponding controller:

```html
<div class="hero-unit">
  <h1>Cheerio!</h1>
  <p>You now have</p>
  <ul>
      <li ng-repeat="thing in awesomeThings">{{thing}}</li>
  </ul>
  <p>installed.</p>
  <h3>Enjoy coding! - Yeoman</h3>
</div>
```

And here's `controllers/main.js`:

```javascript
'use strict';

yeomanTestApp.controller('MainCtrl', function($scope) {
  $scope.awesomeThings = [
    'HTML5 Boilerplate',
    'AngularJS',
    'Testacular'
  ];
});
```

Again, not very interesting; the controller just publishes the list of `awesomeThings` onto the scope and the view makes an unsorted list from them.

But let's say we want to make a seperate view/controller and corresponding route for adding a new item to the list of `awesomeThings`. Normally, you'd have to make these two files, include their script tags into your `index.html`, and then rewrite your route configuration. With Yeoman, you can accomplish all of that with a single command:

```shell
λ → yeoman init angular:route add
```

This will tell Yeoman to generate all the files we need for a route called "add." Enter this command and take a look at the output:

```shell
.. Invoke angular:route ..

.. Invoke angular:controller:route ..
Writing app/scripts/controllers/add.js...OK
Writing test/spec/controllers/add.js...OK

.. Invoke angular:view:route ..
Writing app/views/add.html...OK
```

Invoking `angular:route` also invoked `angular:controller` and `angular:view`. You can actually run either of these seperatly as well, in which case they will not modify `yeoman-test.js` to add a route. Also note that `angular:controller` added two files: the controller implementation in `app/scripts/controllers/add.js` as well as a unit spec in `test/spec/controllers/add.js`. We'll talk about testing more in a bit, but for now, let's see what the script block of `index.html` looks like now:

```html
<!-- build:js scripts/scripts.js -->
<script src="scripts/yeoman-test.js"></script>
<script src="scripts/controllers/main.js"></script>
<script src="scripts/controllers/add.js"></script>
<!-- endbuild -->
```

It added the script tag, just as expected. Let's check `yeoman-test.js` to make sure all is well there:

```javascript
'use strict';

var yeomanTestApp = angular.module('yeomanTestApp', [])
  .config(['$routeProvider', function($routeProvider) {
    $routeProvider
      .when('/', {
        templateUrl: 'views/main.html',
        controller: 'MainCtrl'
      })
      .when('/add', {
        templateUrl: 'views/add.html',
        controller: 'AddCtrl'
      })
      .otherwise({
        redirectTo: '/'
      });
  }]);
```

Yep, we have a new route.

It's worth noting that Yeoman isn't rewriting these files; it carefully edits them in a non-destructive way. This means that you can create your own routes, add your own files, and still use Yeoman without fear that it's going to screw up your custom configurations somehow (but you're using source control anyway, so that wouldn't matter, right? ;P).

### Testing

Testing with AngularJS is already easy, but Yeoman makes it even easier still. When you use Yeoman to generate a file for you, it will also generate a stubbed out [Jasmine](http://pivotal.github.com/jasmine/) unit test for [Testacular](http://vojtajina.github.com/testacular/) as well. Notice when we made a new route above, it created a unit spec as well. Let's open `test/spec/controllers/main.js` and see what it looks like:

```javascript
'use strict';

describe('Controller: MainCtrl', function() {

  // load the controller's module
  beforeEach(module('yeomanTestApp'));

  var MainCtrl,
    scope;

  // Initialize the controller and a mock scope
  beforeEach(inject(function($controller) {
    scope = {};
    MainCtrl = $controller('MainCtrl', {
      $scope: scope
    });
  }));

  it('should attach a list of awesomeThings to the scope', function() {
    expect(scope.awesomeThings.length).toBe(3);
  });
});
```

Yeoman has created all of the boilerplate setup and teardown that a controller test would need. From here, you can easily add more tests by adding additional `it` and `describe` statements. For more on testing with Testacular, I recommend watching my colleague [Vojta Jina's excellent screencast](http://www.youtube.com/watch?v=MVw8N3hTfCI).

If you haven't yet installed Testacular, you can get it through NPM accordingly:

```shell
λ → npm install -g testacular
```
You can run the test as you usually would with Testacular, like this:

```shell
λ → testacular start
```

This will run Testacular in "watch mode" and have it automatically run the tests whenever you update the files. Alternatively, you can do a single run through Yeoman like this:

```shell
λ → yeoman test
```

which is just an alias for:

```shell
λ → testacular start --single-run
```

All of the typical pains of setting up the test runner and configuring it are taken care of by Yeoman. Testacular makes cross browser testing a breeze (just add additional browsers to your config file), and AngularJS gives you a clear seperation of concerns so you can write concise, focused tests. With these great tools, there's really no excuse not to unit test your code.

### Building

To build the production-ready version of your app, simply run:

```shell
λ → yeoman build
```

Yeoman will automatically concatinate the scripts between `<!-- build:js scripts/scripts.js -->` and `<!-- endbuild -->`, version the file names, and update the references to the versioned files in your html. Automatic minification is not yet implemented, but it's coming soon!

Yeoman can also be configured to optimize images and do whatever else your application needs to be blazing fast. The build system is based on [Grunt](http://gruntjs.com/), which has many additional plugins and is highly extensible.

### Conclusion

There's a lot more to Yeoman, so if you're interested, you should definitely [check out the documentation](https://github.com/yeoman/yeoman#welcome-friend). Have ideas to improve the Yeoman AngularJS code generators? Check out the [Yeoman Generator Github repo](https://github.com/yeoman/generators/) and get hacking!
