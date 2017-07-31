## Step 1
### Set up project with necessary JavaScript, jQuery, and HTML

## Step 2
### Split JavaScript into business logic and UI logic
_This will entail linking the files by using Node modules with the special keyword called `exports`_
* create js folder
  * move UI logic into js/pingpong-interface.js
  * be sure to include extra script tag for interface file so browser has access to both

### Creating a new object to be instantiated
* make pingPong a method of the calculator object
````
**js/pingpong.js**
function Calculator(skinName) {
  this.skin = skinName;
}
````

````
**js/pingpong-interface.js**
var simpleCalculator = new Calculator("hot pink");
    var output = simpleCalculator.pingPong(goal);
````
````
* add node to pingpong.js to allow for completion of node module for Calculator `exports.calculatorModule = Calculator;`
````

````
* add calculatorModule to recieving file *js/pingpong-interface.js* `var Calculator = require('./../js/pingpong.js').calculatorModule;`
````

_**See initial-flow file for more on this**_

## Step 3
### Initializing npm
````
$ npm init
````
* _This creates a manifest file which is where npm stores a list of packages needed for the project, along with which versions we need._
* _For name we can type ping-pong and then press enter. Other than that, we can press enter at each prompt to leave all the defaults.

### Installing npm Packages
````
$ npm install gulp --save-dev
````
* _Gulp is a JavaScript package that runs development tasks for us._

````
$ npm install browserify --save-dev
````
* _The browserify package is responsible for using these keywords "export" and "require" to translate the code into JavaScript our browser does understand. It follows each file path used by require like a treasure map and collects all the code into some less readable code that will make sense to our browser._


## Step 4
### Git ignore
````
**.gitignore**
node_modules/
.DS_Store
````

## Step 5
### Installing Gulp
````
$ npm install gulp --save-dev
````
* _Already did this to install gulp in our project folder
````
$ npm install gulp -g
````
* _need to install gulp globally to use it in the terminal. We do this by using a new flag: -g for "global"._

````
$ sudo npm install gulp -g
````
* _In case of permission errors use this

## Step 6
### create gulpfile.js

````
var gulp = require('gulp');
````
* _We use the require function again (just as we did in our pingpong-interface.js file) in order to load the gulp package into a gulp variable that can be used in our code._

````
**Example gulpfile task **
gulp.task('myTask', function(){
  console.log('hello gulp');
});
````
````
** breakdown of gulpfile task**
Here, we are calling the task method defined in the gulp package to create a task. This method takes two arguments:
The first is a string representing the name of the task, so we can refer to it later.
The second is the function to run when we tell gulp to run this task. In this case the task just prints "hello gulp" to the console.
````
````
$ gulp myTask
````
* _to run the simple command in terminal_

## Step 6
### Using browserify with gulp
````
$ npm install vinyl-source-stream --save-dev
````
````
**gulpfile.js**
var browserify = require('browserify');
var source = require('vinyl-source-stream');
////include both the browserify package we downloaded and the new vinyl-source-stream package at the top of our gulpfile. vinyl-source-stream is used for putting the browserified source code into a new file.


````
````
gulp.task('jsBrowserify', function() {
  return browserify({ entries: ['./js/pingpong-interface.js'] })
    .bundle()
    .pipe(source('app.js'))
    .pipe(gulp.dest('./build/js'));
});

///First, we call the browserify function and instruct which files to browserify. This is done by passing in an object with a key entries. Its corresponding value is an array of file names. We tell it to pull in our front-end file only, not our back-end pingpong.js file because that was taken care of by the require keyword in our pingpong-interface.js file when we wrote var Calculator = require('./../js/pingpong.js').calculatorModule;.

Then, we call bundle(), which is built into the Browserify package. Don't worry about exactly what this does, just know it's part of the Browserify process for now.

Finally we tell it to create a new file called app.js, and to put it in a new folder, which we will name build. Then inside of there, we want to put app.js into another new folder, which we will name js.
````

````
$ gulp jsBrowserify
This will generate the build folder, the same way the browserify command did in the terminal.
````

**Now that our JavaScript is going into a build folder, into a new file js/app.js, we need to replace our old <script> tags with a new one to load our generated build/js/app.js file. See below**

````
**index.html**<!DOCTYPE html>
<html>
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.0/jquery.min.js"></script>
    <script type="text/javascript" src="build/js/app.js"></script>
    <title>Ping Pong</title>
  </head>
  <body>
    <form id="ping-pong-form">
      <label for="goal">Enter a number:</label>
      <input id="goal" type="number">
      <button type="submit">Submit</button>
    </form>
    <ul id="solution"></ul>
  </body>
</html>
`````


## Step 7
### Concatenation
* Say we want to add an email list to our application

##### Step 1: add form to html
````
<!DOCTYPE html>
<html>
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.0/jquery.min.js"></script>
    <script type="text/javascript" src="build/js/app.js"></script>
    <title>Ping Pong</title>
  </head>
  <body>
    <form id="ping-pong-form">
      <label for="goal">Enter a number:</label>
      <input id="goal" type="number">
      <button type="submit">Submit</button>
    </form>
    <form id="signup">
      <label for="email">Enter your email:</label>
      <input id="email" type="text">
      <button type="submit">Submit</button>
    </form>
    <ul id="solution"></ul>
  </body>
</html>
````

##### Step 2 : add file to process form
````
$(document).ready(function(){
  $('#signup').submit(function(event){
    event.preventDefault();
    var email = $('#email').val();
    $('#signup').hide();
    $('#solution').prepend('<p>Thank you, ' + email + ' has been added to our list!</p>');
  });
});
````
````
$ npm install gulp-concat --save-dev
````

##### Step 3: Add this to gulpfile.js
````
...

var concat = require('gulp-concat');

...

gulp.task('concatInterface', function() {
  return gulp.src(['./js/pingpong-interface.js', './js/signup-interface.js'])
    .pipe(concat('allConcat.js'))
    .pipe(gulp.dest('./tmp'));
    });

//// Here, we have created a task called 'concatInterface'. It uses gulp.src to pull in all the files used in the browser. These files are formatted as an array of file names we are passing in.

The next line calls our concat() function, created with require at the top. We pass it the name of the file we want it to create, allConcat.js.

Next, we use gulp.dest to tell gulp where to save our new file, which contains both of our JavaScript files. We're going to put it inside of a folder called tmp, which stands for temporary. This is because allConcat.js will not be used in the browser. First, we have to browserify it to pull in any modules it uses.
````
````
gulp.task('jsBrowserify', ['concatInterface'], functio() {
  return browserify({ entries: ['./tmp/allConcat.js'] })
    .bundle()
    .pipe(source('app.js'))
    .pipe(gulp.dest('./build/js'));
});
//// modify the browserify task to do this
````

````
$ gulp jsBrowserify
////run new version of browserify task
`````


## Step end
````
**gulpfile.js**
gulp.task('concatInterface', function() {
  return gulp.src(['./js/*-interface.js'])
    .pipe(concat('allConcat.js'))
    .pipe(gulp.dest('./tmp'));
});
///// We can shorten our new concatenation task even more by using a globbing pattern using *, the wildcard symbol. We can tell the gulp-concat package to concatenate and browserify all files inside of our js folder that end in the string -interface.js.

//// If we maintain this naming convention as our project grows we won't need to modify our gulp concatenate/browserify tasks if we add new files - we just name them something ending in -interface.js if they are going to be used in the browser, and keep them in the js folder. Then they will automatically be included in build/js/app.js.
````


## Step minification

````
$ npm install gulp-uglify --save-dev
````
````
var uglify = require('gulp-uglify');
````
````
gulp.task("minifyScripts", ["jsBrowserify"], function(){
  return gulp.src("./build/js/app.js")
    .pipe(uglify())
    .pipe(gulp.dest("./build/js"));
});
````
````
$ gulp minifyScripts
````
