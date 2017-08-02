#Lecture 1-3 RECAP! :)

## Week 1: HTML/CSS
#### HTML Files
- Start with <!DOCTYPE html> and then <html>
- <meta charset=utf-8> makes the data UTF-8, and therefore the validator does not need to determine the character encoding of your document, and will ignore any charset information specified

#### CSS Files
- Inline style= overrides Embedded <style> overrides External .css

#### Multicolumn layout
- With CSS:
    <div id="centreContent>
        <div id="leftColumn"> </div>
        <div id="centreColumn"> </div>

- With Bootstrap:
    <div class="col-sm-3"> & <div class="col-sm-9">
    Sizes:
    - .col-xs-$ Extra Small (Phone **< 768px**)
    - .col-sm-$ Small devices (Tablets **>= 768px**)
    - .col-md-$ Medium devices (Desktops **>= 992px**)
    - .col-lg-$ Large Devices (Large Desktops **>= 1200px**)

#### HTTP Methods:
- If GET is used the form data is sent as URL parameters
    - Can be BOOKMARKED
    - Reload = same action occurs twice
    - Use when: retrieving information ie. search results
URL: http://example.com/action.php?firstname=bill&surname=smith&password=abc123
- If POST is used the form data is sent in the body of the HTTP request
    - Requests hide sensitive information 
    - Reload = browser asks if they want to "resend the form data".
    - Use when: updating the server, upload big data ie. images
URL: http://example.com/action.php
Request body: firstname=bill&surname=smith&password=abc123

<button type="submit/reset/button"> for initiating actions or submitting the form

## THE WEB
The Web runs on HTTP, TCP/IP protocols
GET: return data at URL
POST: Send data in request body
DELETE: delete data at URL (semantic)

Three tier archiecture:
- Presentation (i.e. Client)
- Logic (i.e. web server)
- Data (i.e. MySQL, SQLite)

## THE DEVELOPMENT ENVIRO // RESPONSIVE WEB DESIGN
#### Viewport scaling
If we design a mobile a specific site we need a way to disable the device's sacling.
- One approach: Set the initial-scale property to 1.0:
```<meta name="viewport" content="initial-scale=1.0">```

#### Bootstrap
- Uses a 12 column grid system for layout

## Week 2: PHP - Part 1
#### What is PHP?
- A scripting & templating language

#### PHP Syntanx
- Semicolons
- $variables
- $string = "Simon"
- Strings in PHP preserve new line characters but HTML do not (use <br>)
- String concatenation
    echo "Meow".$catName
    echo "Meow", $catName
- If a string contains no variables, single quotes should be used

#### Arrays
- $array = array ()
or
- $array = []
- If no index value is specified, it is added to the end of the array
__Array Size__
- count()
__Maps__
- keys: either integer or string
- values: any PHP type
- also can be initialised with key => value pairs:
    $person = array('name'=>'Tom','age'=>21);
__Join(separator, array)__
- Creates a string containing all elements of the array separated by the string specified

#### Built-in Functions
* array()
* count()
* var_dump()
* exit()
* phpinfo() - shows which extension is supported