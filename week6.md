# PHP Database Access 
* Accessing an SQLite database from PHP 
* Executing queries
* Processing Results
* List-detail pattern
* CRUD patterm
* SQL sanitisation 

## SQLite vs MySQL
**MySQL**
* Runs the database server as a separate process, and allows the system to scale.
* The database server can be assigned to a different core, processor, or machine, or even be distributed across several machines.
**SQLite**
* In contrast to a database server, an API-based database doesn't run as a separate processs. 

Database Server 
Web App <--> API <--> Database Server <--> Database File

API-based Database
Web App <--> API <---> Database File

## Programming with databases
There are two approaches to build a web application using PHP that uses a database:
- Object-relational mapping
- Database API

* Object relational mapping is used by web frameworks such as Laravel and partially avoid SQL, using a similar object-function syntax instead.

### PHP Data Objects (PDO)
* Accessing a database involves give steps:
1. Conntecting to the SQL server.
2. Selecting the datase to access.
3. Executing the query or command.
4. Processing the results (if any).
5. Close the connection. 
* When using RDBMS specific APIs all of the preceding steps must be performed. 

* PDO combines the first two steps into one (note also that SQLite doesn't have a database server to connect to).
* PDO also automatically takes care of closing the connection.

**Creating a Connecton**
* Every time a web request is made, a PHP script must make a new connection with the SQL server (the connections can not be preserved between requests).
* Creating a new PDP connection is performed by creating a new PDO object:
```php
$db = new PDO(connection_string);
```
Note: The connection string will differ based on the type of database.

**Creating a MySQL connection:**
* A mySQL connection will require a host name, database name, username and password, e.g.:
```php
$db = new PDO('mysql:host=localhost;dbname=test', $username, $password)
```
**Creating an SQLite connection:**
* A SQLite connection only requires the path to the database, e.g. If the database is in a "db" subfolder:
```php
$db = new PDO('sqlite:db/test.sqlite');
```
Note: If the file test.sqlite doesn't exist SQLite will create it.

**Handling errors**
* PDO has three different modes for handling errors:
- SILENT - default mode where errors are detected via return values
- WARNING - a traditional warning is emited, useful when debugging
- EXCEPTION - an exception is thrown on an error
* We will use the EXCEPTION mode in this course for the following reasons:
- Silent mode requires that the return value of every database call is checked.
- Exception handlind is generally good practice as it ensures that every error is detected and it moves error handling to the bottom of the function.
* We need to activate exception handling on our database connection:
```php
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```
* We can end the execution of our PHP early by calling the die() function. 
* The die() function can also take a string which will be echoed to the output.
* Conntecting to a database and handling an exception:
```php
try{
    $db = new PDO('sqlite:db/test.sqlite');
    $db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e){
    die("Error:".$e->getMessage());
}
```
**A db_defs.php file** 
* One of the big advantages of PDO is the ability to switch RDBMS without affecting the rest of the code. 

An example db_defs.php file:
```php
<?php
function db_open(){
    try{
        $db = new PDO('sqlite:db/daabase.sqlite');
        $db -> setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    } catch (PDOException $e){
        die("Error: ".$e -> getMessage());
    }
    return $db;
}
>
```
This function would be called as follows:
```php
$db = db_open();
```
**Web application structure**
* As many different types of files are required to build a web app, a well organised directory structure is required.
* A web app could have the following structure (note this is if you aren't using laravel):

    web application directory/
        **includes** subdirectory/
            **db_defs.php** - functions relating to SQL such as connecting and handling errors.
            **defs.php** - other web app functions.
        **db** subdirectory/
            **database.sqlite** - the SQLite database
        **sql** subdirectory/
            **database.sql** - the SQL definitions file
        other web app files

### Laravel SQLite Setup
* Update database configuration file located in: config/database.php file by changing (around line 16):
```php
'default' => env('DB_CONNECTION', 'mysql'), 
```
TO
```php
'default' => env('DB_CONNECTION', 'sqlite'),
```

* Replace MySQL setting in the file .env (under project's root directory)
```sql
DB_CONNECTION = mysql
DB_HOST = 127.0.0.1
DB_PORT = 3306
DB_DATABASE = homestead
DB_USERNAME = homestead
DB_PASSWORD = secret
```
WITH
```sql
DB_CONNECTION = sqlite
```

### Executing Queries
**The DB Class**
* Laravel provides a class called DB which is used to send commands to the database.
* There are different functions for different SQL commands:
```sql
DB::select()
DB::insert()
DB::update()
DB::delete()
```
* Use the select() function to execute a query:
```sql
resultSet = DB::select(query);
```
E.g.:
```sql
sql = "select * from item";
$result = $DB::select($sql);
```
* Note: DB::select() will always return an array of results

**Processing results**
Generally, we can pass the results from the DB::select() function to a view for display:
```sql
Route::get('item_list', function(){
    $sql = "select * from item";
    $items = DB::select($sql);
    return view('items.item_list') -> with('items', $items);
});
```
**Displaying Results**
* Results can be displayed in the view using a Blade **forelse** loop:
```php 
@section('content')
<ul>
@forelse($items as $item)
    <li>{{$item -> summary}} : {{$item -> details}} </li>
@empty
    <p> No items found. </p>
@endforelse
</ul>
@endsection 
```
### SQL Sanitisation 
* SQL queries executed by the web application often include strings entered by hte user.
* It is possible that the user could enter special characters (intentionally or accidently) which changes the function of the SQL query.
* This is known as an **SQL injection** attack.
* For example, a query to search your private messages for messages that came from a particular sender may be formed as follows:
```sql
$sql = "select * from messages
        where user_id = $user_id
            and sender = $sender";
```
**SANITISATION**
* The first step to prevent the previous error is to wrap $sender in single quotes, e.g.:
```sql
$sql = "select * 
        from messages 
        where user_id = 'user_id' and sender ='$sender'";
```
* If $sender contained "Henry or true" then the resulting query string would be:
```sql
select * 
from messages
where user_id = '$user_id' and sender = 'Henry or true';
```
However, even the single quotes can be circumvented with the following:
    search.php?sender=Henry' or "='
* Inserting into the query string becomes:
```sql
select *
from messages
where user_d = '$user_id' and sender = 'Henry' or "=";
```
* The solution to this prolem is to escape any characters in the input string that could affect the query, these include:
- \x00(null), \n, \r, ', ", \x1a (^Z)

* If the input string is escaped, the query becomes:
```sql
select *
from messages
where user_id = '$user_id' and sender = 'Henry or \'\'=\"
```
**SQL escaping** should be used for all data that was entered by the user (or retrieved from GET and/or POST parameters) before including in an SQL string

* If we use DB::select() function and variable expansion our values are not automatically escaped.
* However, DB::select() will escape values if we use '?' placeholders.

**'?' Placeholders**
* Placeholders can be used to make the SQL statement easier to read and provides sanitisation of values.
* When using placeholders, quotes within the query are not required and in fact will generate an error. 

Using placeholders, the above query string would be written as:
```sql
$sql = "select * from messages
        where user_id = ? and sender = ?";
```
* Values to replace the placeholders are passed in an array as the second parameter to DB::select()
```sql
$result = DB::select($sql, array($user_id, $sender));
```
* DB::select() will escape the values in the aray and insert them in the query string in the order of the '?'

### List-Detail Pattern
* A common web app pattern is lis-detail, i.e, a list of items is presented to the user, clicking an item displays a detail page.
* A key concept in the list-detail pattern is the ability of the list page to communicate to the detail page which item has been clicked.
* This is usually achieved through passing the id of the clicked item to the detail page.
* This is an extract of the rendered HTML source of the **item_list.blade.php** view:

```html
<ul>
    <li><a href="item_detail/1"> Cannon PowerShot A590 IS</a></li>
    <li><a href="item_detail/2"> Cannon EOS 550D IS</a></li>
    <li><a href="item_detail/3"> Cannon EOS 50D IS</a></li>
</ul>
```
* Each link has the corect id for the item listed.
* The first step in the list-detail pattern is to output a list of links which include the item's id in the parameter.
* Below is an abstract from the items/items_list.blade.php view:
```php
@section('content')
@if ($items)
<ul>
    @foreach ($items as $item)
    <li><a href="{{ url("item_detail/$item->id") }}">{{ $item->summary }}</a></li>
    @endforeach
</ul>
@else
<p>No items found.</p>
@endif
@endsection
```
Note: relative URL can be generated by "/item_details/{{$item->id}}"

Steps that the item_detail route must complete:
1. Retrieve the id from the URL
2. Perform a SELECT query to retrieve the item with the specified id
3. Pass the resulting item row data to the view
4. Dsplay the results in the view


### CRUD Pattern
* Crud stands for:
- Create
- Retrieve
- Update
- Delete 

**CREATE**
* Creating involves inserting new records into tables.
* The data is generally entered by a user in a form and processed by an action script.

* An extract of the view file: **items/add_item.blade.php**
```php
<form method="post" action="/add_item_action">
    {{ csrf_field() }}
    <p>
        <label> Summary: </label> <br>
        <input type="text" name="summary">
    </p>
    <p>
        <label> Details: </label> <br>
        <textarea name="details"></textarea>
    </p>
    <input type="submit" value="Add item">
</form>
```
* The **add_item_action** will receieve two fields: summary and details.

**add_item_action route in routes/web.php**
```php
Route::post('add_item_action', function()
{
    $summary = request('summary');
    $details = request('details');

    $id = add_item($summary, $details);

    if ($id){
        return redirect("/item_detail/$id");
    } else {
        die("Error adding item");
    }
}
);
```
Note: the POST action of the form submits the summary and details as variables, hence we need to use the input class to access them.
* The add_item() function adds the new item and returns the id for the new record.
* If the item was successfully added, the item_detail page is shown for the newly added item(by redirecting to it, avoiding the reload problem).

**add_item()**
```php
function add_item($summary, $details){
    $sql = "insert into item(summary, details) values(?,?)";
    DB::insert($sql, array($summary, $details));
    $id = DB::getPdo()->lastInsertId();
    return $id;
}
```

**RETRIEVE**
* Retrieval can encompass many aspects.

**UPDATE**
* An update is made when changes are required to an existing record in a table.
* This is ofen the result of the user updating information about an item in the system. 
* Update is similar to Create, and sometimes may even reuse the same form.
* The differences between Update and Create are:
|CREATE  |UPDATE | 
|--------|-------|
|        |       |

**DELETE**
* Deleting simply requires the id of the record to be deleted.
* It is possible that the record has other related records in other tables that must also be deleted
* It may be beneficial to delete the dependent records first to maintain referential integrity.
* The delete item function from the items example:
```php
function delete_item($id){
    $sql = "delete from item where id = ?";
    DB::delete($sql, array($id));
}
```