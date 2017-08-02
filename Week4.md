# Web Framework
## Concepts: 
* Web Framework Concepts
* Laravel
* Routes
* Views 
* Templating
    * Templating Languages
    * Blade templating
    * Blade syntax
    * Template inheritance 

#### Web Framework Concepts
Web frameworks provide APIs and commands that automate common web app tasks.
This modern framework approach was pioneered by **Ruby on Rails**. 

#### Laravel 
Laravel is an open-source PHP MVC web framework (2011). 

Creating a project: 
```php
    composer create-project --prefer-dist laravel/laravel=5.4.* project-name
```
PHP's built in web server:
Command -
```php
    php artisan serve --host $IP --port $PORT
```

#### Routes
Maps between URL and code (generally functions)
Declaring a Route:
```php
    Route::get('/', function(){
        return view('greeting');
    });
```
Generic declartion:
```php
    Route::http_method(path, function)
```
Route class has static functions for declaring routes based on the HTTP method URI: get(), post(), put(), path(), delete(), options()
Functions that respond to multiple HTTP methods:
- match() pass in an array of HTTP methods as strings, e.g. array ('GET', 'POST')
- any() respondes to any HTTP method

#### Views
Views are a way to separate presentation logic from controller domain object
Variables can be passed to views several ways
```php
    view('hello', array('name'=>$name))
    view('hello')->with('name', $name)
    view('hello')->withName($name)
```
There is no actual method define withName() however, it defaults to __call()
* The _call() method can then interpret the method name and handle it

## Blade templates 
- File: .blade.php
- view('hello') don't specify extension (this loads hello.blade.php or hello.php if that doesn't exist)
- Output variables using {{$variable}}
- If you don't want the contents of the variable escaped then only use {!! $variable !!}

- {{csrf_field()}} is laravels built in function to guard against cross-site request forgery (CSRF) attacks.

## Blade Syntax
**Comments** 
{{-- --}}
May span multiple lines

**Flow Control**
```php 
    @if(condition)
    @elseif(condition)
    @else
    @endif
```
PHP vs BLADE.PHP
**PHP Version**
```php
    <?php if (!empty($error)){?>
    <p class="alert"><?=$error?> </p>
    <?php } ?>
```
**Blade version**
```php 
    @if(!empty($error))
    <p class="alert">{{$error}} </p>
    @endif
```
**Foreach Loop**
```php
    @foreach(array as variable)
    @endforeach
```
```php
    @forelse(array as variable)
    @empty
    @endforelse
```
**Example forelse Loop**
```php
    @forelse($names as $name)
        <li>{{$name}} </li>
    @empty
        No names.
    @endforelse
```

**For loop**
```php
    @for ($i=1; $i <= 5; $i++)
        {{ $i }}
    @endfor
```
## Template Inheritance 
#### Blade Template Inheritance
```php
    @yield = keyword in layouts to indicate a section that is to be overwritten by a child template.
    @extends = child templates indicate the parent template they will extend.
    @section = defines the contents of @yield
    End an @section with @endsection. 
```

#### Debugging Tips
1. Use the error log to help identify errors. Laravel's error log can be found at: storage/logs
2. When you try to execute a php artisan command, but recieve this following error: 
```php
[Symfony\Component\Debug\Exceptio
n\FatalThrowableError]
Parse error: syntax error, unexpected e
nd of file, expecting ',' or ')'
```
One possible cause could be a syntax error in your route file.