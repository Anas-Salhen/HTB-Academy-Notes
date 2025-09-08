# Back End Servers
It is the hardware on the back end that hosts all the applications necessary to run the web app. It fits in the [Data access layer](https://en.wikipedia.org/wiki/Data_access_layer). Back end servers contain 3 main components:
- Web Server
- Database
- Development Framework
There are many popular combinations of "stacks" for back-end servers, which contain a specific set of back end components. Some common examples include:

| Combinations                                                        | Components                                         |
| ------------------------------------------------------------------- | -------------------------------------------------- |
| [LAMP](https://en.wikipedia.org/wiki/LAMP_\(software_bundle\))      | `Linux`, `Apache`, `MySQL`, and `PHP`.             |
| [WAMP](https://en.wikipedia.org/wiki/LAMP_\(software_bundle\)#WAMP) | `Windows`, `Apache`, `MySQL`, and `PHP`.           |
| [WINS](https://en.wikipedia.org/wiki/Solution_stack)                | `Windows`, `IIS`, `.NET`, and `SQL Server`         |
| [MAMP](https://en.wikipedia.org/wiki/MAMP)                          | `macOS`, `Apache`, `MySQL`, and `PHP`.             |
| [XAMPP](https://en.wikipedia.org/wiki/XAMPP)                        | Cross-Platform, `Apache`, `MySQL`, and `PHP/PERL`. |

# Web Servers
A [web server](https://en.wikipedia.org/wiki/Web_server) is an application that runs on the back end server, which handles all of the HTTP traffic from the client-side browser, routes it to the requested pages, and finally responds to the client-side browser.

The web server accepts HTTP requests from the client side and sends different HTTP responses with different codes like:

| Code                        | Description                                                                                                         |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Successful responses**    |                                                                                                                     |
| `200 OK`                    | The request has succeeded                                                                                           |
| **Redirection messages**    |                                                                                                                     |
| `301 Moved Permanently`     | The URL of the requested resource has been changed permanently                                                      |
| `302 Found`                 | The URL of the requested resource has been changed temporarily                                                      |
| **Client error responses**  |                                                                                                                     |
| `400 Bad Request`           | The server could not understand the request due to invalid syntax                                                   |
| `401 Unauthorized`          | Unauthenticated attempt to access page                                                                              |
| `403 Forbidden`             | The client does not have access rights to the content                                                               |
| `404 Not Found`             | The server can not find the requested resource                                                                      |
| `405 Method Not Allowed`    | The request method is known by the server but has been disabled and cannot be used                                  |
| `408 Request Timeout`       | This response is sent on an idle connection by some servers, even without any previous request by the client        |
| **Server error responses**  |                                                                                                                     |
| `500 Internal Server Error` | The server has encountered a situation it doesn't know how to handle                                                |
| `502 Bad Gateway`           | The server, while working as a gateway to get a response needed to handle the request, received an invalid response |
| `504 Gateway Timeout`       | The server is acting as a gateway and cannot get a response in time                                                 |
## Popular Web Servers
### Apache
[Apache](https://www.apache.org/) 'or `httpd`' is an open-source project and currently is the most common web server on the internet, hosting more than 40% of all internet websites. Apache usually comes pre-installed in most Linux distributions.

Apache is usually used with `PHP` for web application development, but it also supports other languages like `.Net`, `Python`, `Perl`, and even OS languages like `Bash` through CGI.  
Some big companies utilize Apache including Apple, Adobe and Baidu.

### NGINX
The second most common web server on the internet. NGINX focuses on serving many concurrent web requests with relatively low memory and CPU load by utilizing an async architecture to do so. This reliability makes it the most popular web server among high traffic websites, with around 60% of the top 100,000 websites using NGINX. It is also free and open-source. Companies that use NGINX include Google, Facebook, Twitter, Cisco, Intel, Netflix and HackTheBox.

### IIS
[IIS (Internet Information Services)](https://en.wikipedia.org/wiki/Internet_Information_Services) is the third most common web server on the internet and is developed and maintained by Microsoft and mainly runs on Windows Servers.

IIS is usually used to host web applications developed for the Microsoft .NET framework, but can also be used to host web applications developed in other languages like `PHP`, or host other types of services like `FTP`. Furthermore, IIS is very well optimized for Active Directory integration. Some popular organizations that utilize it include Microsoft, Office365, Skype, Stack Overflow and Dell.

### Others
Aside from the 3 mentioned earlier there are other web servers like [Apache Tomcat](https://tomcat.apache.org/) for `Java` web applications, and [Node.JS](https://nodejs.org/en/) for web applications developed using `JavaScript` on the back end.

# Databases
Most developers look for certain characteristics in a database, such as speed in storing and retrieving data, size when storing large amounts of data, scalability as the web application grows, and cost.

## Relational (SQL)
[Relational](https://en.wikipedia.org/wiki/Relational_database) (SQL) databases store their data in tables, rows, and columns. Each table can have unique keys, which can link tables together and create relationships between tables.  
<img width="735" height="479" alt="Pasted image 20250907230859" src="https://github.com/user-attachments/assets/a50d3671-c3a0-4772-90eb-96d8e9fa3d10" />  
The relationship between tables within a database is called a Schema. In the example above the `id` column from the `users` table was used as a key to link the `id` column to the `user_id` column in the `posts` table. These links make retrieving and managing data fast and efficient. Some of the most common relational databases include:

| Type                                                        | Description                                                                                                                                                                                   |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [MySQL](https://en.wikipedia.org/wiki/MySQL)                | The most commonly used database around the internet. It is an open-source database and can be used completely free of charge                                                                  |
| [MSSQL](https://en.wikipedia.org/wiki/Microsoft_SQL_Server) | Microsoft's implementation of a relational database. Widely used with Windows Servers and IIS web servers                                                                                     |
| [Oracle](https://en.wikipedia.org/wiki/Oracle_Database)     | A very reliable database for big businesses, and is frequently updated with innovative database solutions to make it faster and more reliable. It can be costly, even for big businesses      |
| [PostgreSQL](https://en.wikipedia.org/wiki/PostgreSQL)      | Another free and open-source relational database. It is designed to be easily extensible, enabling adding advanced new features without needing a major change to the initial database design |
Other common SQL databases include: SQLite, MariaDB, Amazon Aurora, and Azure SQL.

## Non-relational (NoSQL)
A [non-relational database](https://en.wikipedia.org/wiki/NoSQL) does not use tables, rows, columns, primary keys, relationships, or schemas instead it stores data using various storage models, depending on the type of data stored.

Due to the lack of a defined structure for the database, NoSQL databases are very scalable and flexible. When dealing with datasets that are not very well defined and structured, a NoSQL database would be the best choice for storing our data.

There are 4 common storage models for NoSQL databases:
- Key-Value
- Document-Based
- Wide-Column
- Graph
For example, the Key-Value model usually stores data in JSON or XML, and has a key for each pair, storing all of its data as its value:  
<img width="878" height="305" alt="Pasted image 20250907231522" src="https://github.com/user-attachments/assets/3139f93d-4f43-4a98-938e-2cef17509c70" />  
The above example can be represented using JSON as follows:  
```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}
```

The Document-Based model stores data in complex JSON objects and each object has certain meta-data while storing the rest of the data similarly to the Key-Value model. Most common NoSQL databases include:

| Type                                                               | Description                                                                                                                                                                                    |
| ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [MongoDB](https://en.wikipedia.org/wiki/MongoDB)                   | The most common NoSQL database. It is free and open-source, uses the Document-Based model, and stores data in JSON objects                                                                     |
| [ElasticSearch](https://en.wikipedia.org/wiki/Elasticsearch)       | Another free and open-source NoSQL database. It is optimized for storing and analyzing huge datasets. As its name suggests, searching for data within this database is very fast and efficient |
| [Apache Cassandra](https://en.wikipedia.org/wiki/Apache_Cassandra) | Also free and open-source. It is very scalable and is optimized for gracefully handling faulty values                                                                                          |
Other common NoSQL databases include: Redis, Neo4j, CouchDB, and Amazon DynamoDB.

## Use in Web Applications
Example, within a PHP web application, once MySQL is up and running, we can connect to the database server with:
```php
$conn = new mysqli("localhost", "user", "pass");
```
Then, we can create a new database with:
```php
$sql = "CREATE DATABASE database1";
$conn->query($sql)
```
After that, we can connect to our new database, and start using the MySQL database through MySQL syntax, right within PHP, as follows:
```php
$conn = new mysqli("localhost", "user", "pass", "database1");
$query = "select * from table_1";
$result = $conn->query($query);
```
Then we can use it for many things like when a user uses the search function to search for other users through the database:
```php
$searchInput =  $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
$result = $conn->query($query);
```
Then, the web application sends the result back to the user:
```php
while($row = $result->fetch_assoc() ){
	echo $row["name"]."<br>";
}
```

# Development Frameworks & APIs
Some of the most common web development frameworks include:
- [Laravel](https://laravel.com/) (PHP): usually used by startups and smaller companies, as it is powerful yet easy to develop for.
- [Express](https://expressjs.com/) (Node.JS): used by PayPal, Yahoo, Uber, IBM, and MySpace.
- [Django](https://www.djangoproject.com/) (Python): used by Google, YouTube, Instagram, Mozilla, and Pinterest.
- [Rails](https://rubyonrails.org/) (Ruby): used by GitHub, Hulu, Twitch, Airbnb, and even Twitter in the past.

## Query Parameters
The default method of sending specific arguments to a web page is through `GET` and `POST` request parameters.

For example, a `/search.php` page would take an `item` parameter, which may be used to specify the search item. Passing a parameter through a GET request is done through the URL '`/search.php?item=apples`', while POST parameters are passed through POST data at the bottom of the POST HTTP request.

## Web APIs
An API ([Application Programming Interface](https://en.wikipedia.org/wiki/API)) is an interface within an application that specifies how the application can interact with other applications. For Web Applications, it is what allows remote access to functionality on back end components. Web APIs are usually accessed over the `HTTP` protocol and are usually handled and translated through web servers.

To enable the use of APIs within a web application, the developers have to develop this functionality on the back end of the web application by using the API standards like SOAP or REST.

## SOAP
In the SOAP ([Simple Objects Access Protocol](https://en.wikipedia.org/wiki/SOAP)) standard, HTTP requests and responses both are written in XML. SOAP is very useful for transferring structured data (i.e., an entire class object), or even binary data, and is often used with serialized objects. It is also very useful for sharing stateful objects (i.e., sharing/changing the current state of a web app).

However, SOAP may be difficult to use for beginners or require long and complicated requests even for small/simple queries. This is where the REST API standard is more useful.

## REST
The REST ([Representational State Transfer](https://en.wikipedia.org/wiki/Representational_state_transfer)) standard shares data through the URL path (i.e. `search/users/1`), and usually returns the output in JSON format. REST APIs pass the input through the URL path without specifying a name or type, unlike query parameters. For example this: `GET /users?id=123` when using REST APIs becomes `GET /users/123`. 

This way, REST APIs break the website into smaller, modular endpoints that are easy to explore. We can combine it with query parameters for more filtering and sorting like `GET /users/123/orders?status=pending&sort=date`.

REST APIs usually use JSON though it can be configured to use other formats like XML, x-www-form-urlencoded, or even raw data. REST uses various HTTP methods to perform different actions on the web application:
- GET request to retrieve data
- POST request to create data (non-idempotent)
- PUT request to create or replace existing data (idempotent)
- DELETE request to remove data
