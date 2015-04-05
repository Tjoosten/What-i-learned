PHP Exceptions
===============

In this file we are going to lean about PHP exceptions form the ground up.
These concepts are utilized in many large, scalable and object oriented applications and frameworks.
Take advantage of this language feature to improve your skills as a web application developer.

## An example first

Before we begin with all the explanations, I would like to show an example first.

Let's say you want to calculate the area of a circle, by the givin radius. This function will do that:

```php
function circle_area($radius) {
  return pi() * $radius * $radius;
}
```

It is very simple, however it does not check if the radius is a valid number.
Now we are going to do that, and throw an exception if the radius is a negative number:

```php
function circle_area($radius) {

  // radius can't be negative
  if ($radius < 0) {
      throw new Exception('Invalid Radius: ' . $radius);
  } else {
    return pi() * $radius * $radius;
  }
}
```

Let's see what happens when we call it with a negative number:

```php
$radius = -2;

echo "Circle Radius: $radius => Circle Area: ".
  circle_area($radius) . "\n";
echo "Another line";
```

The script crashes with the following message:

```html
<br />
<b>Fatal error</b>:  Uncaught exception 'Exception' with message 'Invalid Radius: -2' in C:\wamp\www\test\test.php:19

Stack trace:
#0 C:\wamp\www\test\test.php(7): circle_area(-2)
#1 {main}
  thrown in <b>C:\wamp\www\test\test.php</b> on line <b>19</b><br />
```

Since it was a fatal error, no more code execution happened after that.
However you may not always want your scripts to stop whenever an Exception happens.
Luckily, you can `catch` them and handle them.

This time, let's do it an array of radius values:

```php
$radius_array = array(2,-2,5,-3);

foreach ($radius_array as $radius) {
  try {
    echo "Circle Radius: $radius => Circle Area: ".
      circle_area($radius) . "\n";
  } catch (Exception $e) {
    echo 'Caught Exception: ',  $e->getMessage(), "\n";
  }
}
```

Now we get this output:

```
Circle Radius: 2 => Circle Area: 12.566370614359
Caught Exception: Invalid Radius: -2
Circle Radius: 5 => Circle Area: 78.539816339745
Caught Exception: Invalid Radius: -3
```

There are no more errors, and the script continues to run. That is how you catch exceptions.

## What is an Exception?

Exceptions have been around in other object oriented programming languages for quite some time.
It was first adopted in PHP with version 5.

By definition an Exception is `thrown`, when an exceptional event happens.
This could be as simple as a `division by zero`, or any other kind of invalid situation.

```php
throw new Exception('Some error message.');
```

This may sound similar to other basic errors that you have already seen many times. But Exceptions have a different kind of mechanism.

Exceptions are actually objects and you have the option to `catch` them and execute certain code. This is done by using `try-catch` blocks:

```php
try {
  // some code goes here
  // which might throw an exception
} catch (Exception $e) {
  // the code here only gets executed
  // if an exception happened in the try block above
}
```

We can enclose any code within a `try` block. The following `catch` block is used for catching any exception that might
have been thrown from within the try block.
The catch block never gets executed if there were no exceptions.
Also, once an exception happens, the script immediately jumps to the catch block, without executing any further code.

Further in the article we will have more examples that should demonstrate the power and flexibility of using exceptions
instead of simple error messages.

## Exceptions Bubble Up

When an exception is thrown from a function or a class method, it goes to whoever called that function or method.
And it keeps on doing this until it reaches the top of the stack OR is caught.
If it does reach the top of the stack and is never called, you will get a fatal error.

For example, here we have a function that throws an exception.
We call that function from a second function.
And finally we call the second function from the main code, to demonstrate this bubbling effect:

```php
function bar() {
  throw new Exception('Message from bar().');  
}

function foo() {
  bar();
}

try {
    foo();
} catch (Exception $e) {
  echo 'Caught exception: ',  $e->getMessage(), "\n";
}
```

So, when we call `foo()`, we attempt to catch any possible exceptions.
Even though `foo()` does not throw one, but `bar()` does,
it still bubbles up and gets caught at the top, so we get an output saying: `"Caught exception: Message from bar()."`

## Tracing Exceptions

Since exceptions do bubble up, they can come from anywhere. To make our job easier,
the Exception class has methods that allows us to track down the source of each exception.

Let's see an example that involves multiple files and multiple classes.

First, we have a User class, and we save it as user.php:

```php
class User {

  public $name;
  public $email;

  public function save() {
    $v = new Validator();
    $v->validate_email($this->email);

    // ... save
    echo "User saved.";

    return true;
  }
}
```

It uses another class named Validator, which we put in validator.php:

```php
class Validator {
  public function validate_email($email) {
    if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
        throw new Exception('Email is invalid');
    }
  }
}
```

From our main code, we are going to create a new User object, set the name and email values.
Once we call the `save()` method, it will utilize the Validator class for checking the email format, which might return an exception:

```php
include('user.php');
include('validator.php');


$u = new User();
$u->name = 'foo';
$u->email = '$!%#$%#*';

$u->save();
```

However, we would like to catch the exception, so there is no fatal error message.
And this time we are going to look into the detailed information about this exception:

```php
include('user.php');
include('validator.php');

try {

  $u = new User();
  $u->name = 'foo';
  $u->email = '$!%#$%#*';

  $u->save();
} catch (Exception $e) {
  echo "Message: " . $e->getMessage(). "\n\n";
  echo "File: " . $e->getFile(). "\n\n";
  echo "Line: " . $e->getLine(). "\n\n";
  echo "Trace: \n" . $e->getTraceAsString(). "\n\n";

}
```

The code above produces this output:

```
Message: Email is invalid

File: C:\wamp\www\test\validator.php

Line: 7

Trace:
#0 C:\wamp\www\test\user.php(11): Validator->validate_email('$!%#$%#*')
#1 C:\wamp\www\test\test.php(12): User->save()
#2 {main}
```

So, without looking at a single line of code, we can tell where the exception came from.
We can see the file name, line number, exceptions message and more. The trace data even shows the exact lines of code that got executed.

The structure of the default Exception class is shown in the PHP manual, where you can see all the methods and data it comes with:

```php
class Exception {
  protected $message = 'Unknown exception';   // exception message
  private   $string;                          // __toString cache
  protected $code = 0;                        // user defined exception code
  protected $file;                            // source filename of exception
  protected $line;                            // source line of exception
  private   $trace;                           // backtrace
  private   $previous;                        // previous exception if nested exception

  public function __construct($message = null, $code = 0, Exception $previous = null);

  final private function __clone();           // Inhibits cloning of exceptions.

  final public  function getMessage();        // message of exception
  final public  function getCode();           // code of exception
  final public  function getFile();           // source filename
  final public  function getLine();           // source line
  final public  function getTrace();          // an array of the backtrace()
  final public  function getPrevious();       // previous exception
  final public  function getTraceAsString();  // formatted string of trace

  // Overrideable
  public function __toString();               // formatted string for display
}
```

## Extending exceptions

Since this is an object oriented concept and Exception is a class, we can actually extend it to create our own custom exceptions.

For example you may not want to display all the details of an exception to the user.
Instead, you can display a user friendly message, and log the error message internally:

```php
// to be used for database issues
class DatabaseException extends Exception {

  // you may add any custom methods
  public function log() {

      // log this error somewhere
      // ...
  }
}

// to be used for file system issues
class FileException extends Exception {

  // ...

}
```

We just created two new types of exceptions. And they may have custom methods.

When we catch the exception, we can display a fixed message, and call the custom methods internally:

```php
function foo() {
  // ...
  // something wrong happened with the database
  throw new DatabaseException();
}

try {
  // put all your code here
  // ...
  foo();
} catch (FileException $e) {
  die ("We seem to be having file system issues. We are sorry for the inconvenience.");
} catch (DatabaseException $e) {
  // calling our new method
  $e->log();

  // exit with a message
  die ("We seem to be having database issues. We are sorry for the inconvenience.");
} catch (Exception $e) {
  echo 'Caught exception: '.  $e->getMessage(). "\n";
}
```

This is the first time we are looking at an example with multiple catch blocks for a single try block.
This is how you can catch different kinds of Exceptions, so you can handle them differently.

In this case we will catch a DatabaseException and only that catch block will get executed.
In this blog we may call our new custom methods, and display a simple message to the user.

Please note that the catch block with the default Exception class must come last,
as our new child classes are also still considered that class.
For example `DatabaseException` is also considered 'Exception' so it can get caught there if the order is the other way around.

## Handling Uncaught Exceptions

You may not always want to look for exceptions in all of your code, by wrapping everything in try-catch blocks. However, uncaught exceptions display a detailed error message to the user, which is also not ideal in a production environment.

There is actually a way to centralize the handling of all uncaught exceptions so you can control the output from a single location.

For this, we are going to be utilizing the `set_exception_handler()` function:

```php
set_exception_handler('exception_handler');

function exception_handler($e) {
  // public message
  echo "Something went wrong.\n";

  // semi-hidden message
  echo "<!-- Uncaught exception: " . $e->getMessage(). " -->\n";
}

throw new Exception('Hello.');
throw new Exception('World.');
```

The first line instructs PHP to call a given function when an exception happens and is not caught. This is the output:

```html
Something went wrong.
<!-- Uncaught exception: Hello. -->
```

As you can see the script aborted after the first exception and did not execute the second one. This is the expected behavior of uncaught exceptions.

If you want your script to continue running after an exception, you would have to use a try-catch block instead.

### Building a MySQL Exception Class

We are going to finish off this tutorial by building a custom MySQL Exception class that has some useful features, and see how we can use it.

```php
class MysqlException extends Exception {

  // path to the log file
  private $log_file = 'mysql_errors.txt';


  public function __construct() {

    $code = mysql_errno();
    $message = mysql_error();

    // open the log file for appending
    if ($fp = fopen($this->log_file,'a')) {

      // construct the log message
      $log_msg = date("[Y-m-d H:i:s]") . " Code: $code - " . " Message: $message\n";

      fwrite($fp, $log_msg);
      fclose($fp);
    }

    // call parent constructor
    parent::__construct($message, $code);
  }
}
```

You may notice that we put pretty much all of the code in the contructor.
Whenever an exception is thrown, it is like creating a new object, which is why the constructor is always called first.
At the end of the constructor we also make sure to call the parent constructor.

This exception will be thrown whenever we encounter a MySQL error.
It will then fetch the error number, and the message directly from mysql,
and then store that information in a log file, along with the timestamp.
In our code we can catch this exception, display a simple message to the user, and let the exception class handle the logging for us.

For example, let's try to connect to MySQL without providing any user/password information:

```php
try {
  // attempt to connect
  if (!@mysql_connect()) {
    throw new MysqlException;
  }
} catch (MysqlException $e) {
  die ("We seem to be having database issues. We are sorry for the inconvenience.");
}
```

We need to prepend the error suppression operator `(@)`` before the `mysql_connect()`` call so that it does not display the error to the user.
If the function fails, we throw an exception, and then catch it. Only our user friendly message will be show to the browser.

The MysqlException class takes care of the error logging automatically. When you open the log file, you will find this line:

```
[2010-05-05 21:41:23] Code: 1045 -  Message: Access denied for user 'SYSTEM'@'localhost' (using password: NO)
```

Let's add more code to our example, and also provide a correct login info:

```php
try {
  // connection should work fine
  if (!@mysql_connect('localhost','root','')) {
      throw new MysqlException;
  }

  // select a database (which may not exist)
  if (!mysql_select_db('my_db')) {
      throw new MysqlException;
  }

  // attempt a query (which may have a syntax error)
  if (!$result = mysql_query("INSERT INTO foo SET bar = '42 ")) {
      throw new MysqlException;
  }
} catch (MysqlException $e) {
  die ("We seem to be having database issues. We are sorry for the inconvenience.");
}
```

If the database connection succeeds, but the database named `my_db is missing, you will find this in the logs:

```
[2010-05-05 21:55:44] Code: 1049 -  Message: Unknown database 'my_db'
```

If the database is there, but the query fails, due to a syntax error for example, you may see this in the log:

```
[2010-05-05 21:58:26] Code: 1064 -  Message: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''42' at line 1
```
