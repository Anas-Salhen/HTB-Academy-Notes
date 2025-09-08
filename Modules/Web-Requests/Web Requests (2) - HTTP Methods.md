# HTTP Methods and Codes
## Request Methods
| **Method** | **Description**                                                                                                                                                                                                                                                                                                                      |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `GET`      | Requests a specific resource. Additional data can be passed to the server via query strings in the URL (e.g. `?param=value`).                                                                                                                                                                                                        |
| `POST`     | Sends data to the server. It can handle multiple types of input, such as text, PDFs, and other forms of binary data. This data is appended in the request body present after the headers. The POST method is commonly used when sending information (e.g. forms/logins) or uploading data to a website, such as images or documents. |
| `HEAD`     | Requests the headers that would be returned if a GET request was made to the server. It doesn't return the request body and is usually made to check the response length before downloading resources.                                                                                                                               |
| `PUT`      | Creates new resources on the server. Allowing this method without proper controls can lead to uploading malicious resources.                                                                                                                                                                                                         |
| `DELETE`   | Deletes an existing resource on the webserver. If not properly secured, can lead to Denial of Service (DoS) by deleting critical files on the web server.                                                                                                                                                                            |
| `OPTIONS`  | Returns information about the server, such as the methods accepted by it.                                                                                                                                                                                                                                                            |
| `PATCH`    | Applies partial modifications to the resource at the specified location.                                                                                                                                                                                                                                                             |
For a full list of HTTP methods, you can visit this [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods).

## Status Codes
| **Class** | **Description**                                                                                                                  |
| --------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `1xx`     | Provides information and does not affect the processing of the request.                                                          |
| `2xx`     | Returned when a request succeeds.                                                                                                |
| `3xx`     | Returned when the server redirects the client.                                                                                   |
| `4xx`     | Signifies improper requests `from the client`. For example, requesting a resource that doesn't exist or requesting a bad format. |
| `5xx`     | Returned when there is some problem `with the HTTP server` itself.                                                               |
Commonly seen status codes:

| **Code**                    | **Description**                                                                                                                                           |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `200 OK`                    | Returned on a successful request, and the response body usually contains the requested resource.                                                          |
| `302 Found`                 | Redirects the client to another URL. For example, redirecting the user to their dashboard after a successful login.                                       |
| `400 Bad Request`           | Returned on encountering malformed requests such as requests with missing line terminators.                                                               |
| `403 Forbidden`             | Signifies that the client doesn't have appropriate access to the resource. It can also be returned when the server detects malicious input from the user. |
| `404 Not Found`             | Returned when the client requests a resource that doesn't exist on the server.                                                                            |
| `500 Internal Server Error` | Returned when the server cannot process the request.                                                                                                      |

# GET
## HTTP Basic Auth
This type of authentication wouldn't allow us to access the page unless we provide a valid pair of credentials. This can be achieved using a number of ways.

<img width="670" height="228" alt="Pasted image 20250905012422" src="https://github.com/user-attachments/assets/b3fb24b5-a5ea-4294-9923-f27e78150d7e" />  
We can type them when they are requested by the website.

We can use `-u <username:password>` with `curl`:
```shell-session
AnasSalhen@htb[/htb]$ curl -u admin:admin http://<SERVER_IP>:<PORT>/

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

We can also type them directly in the URL as `username:password@URL`:
```shell-session
AnasSalhen@htb[/htb]$ curl http://admin:admin@<SERVER_IP>:<PORT>/

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

```shell-session
AnasSalhen@htb[/htb]$ curl -v http://admin:admin@<SERVER_IP>:<PORT>/
<SNIP>
> Authorization: Basic YWRtaW46YWRtaW4=
<SNIP>
```
Because we are using basic HTTP auth we see that the authorization header is set to `Basic YWRtaW46YWRtaW4=` which is the base64 encoded value of `admin:admin`. If we were using a modern method of authentication (e.g. `JWT`), the `Authorization` would be of type `Bearer` and would contain a longer encrypted token.

Instead of providing credentials we can try to manually set the `Authorization` header using `-H` and see if we gain access.
```shell-session
AnasSalhen@htb[/htb]$ curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/

<!DOCTYPE html
<html lang="en">

<head>
...SNIP...
```

Let's say we are visiting a website that has a search function in it and we want to know where the results are coming from. To do that open devtools under the network tab. In our example we see something like this:  
<img width="1278" height="428" alt="Pasted image 20250905013856" src="https://github.com/user-attachments/assets/a870e5b1-9a6f-49e3-88d7-111e1ae12aa5" />  
So a request is sent to `search.php` and there is also a parameter in it `?search=le`. What if we want to send the same exact request from our terminal? We can try to replicate it using the URL that we see here and the HTTP headers but devtools provides an easier way. Just right-click the request and select `Copy>Copy as cURL`. This will make you copy the command needed to replicate this request, paste it in your terminal. There is also `Copy>Copy as Fetch` for those using the JavaScript Fetch library.

# POST
Unlike HTTP `GET`, which places user parameters within the URL, HTTP `POST` places user parameters within the HTTP Request body. This has three main benefits:
- `Lack of Logging`: As POST requests may transfer large files (e.g. file upload), it would not be efficient for the server to log all uploaded files as part of the requested URL.
- `Less Encoding Requirements`: URLs are designed to be shared, which means they need to conform to characters that can be converted to letters. The POST request places data in the body which can accept binary data. The only characters that need to be encoded are those that are used to separate parameters.
- `More data can be sent`: The maximum URL Length varies between browsers (Chrome/Firefox/IE), web servers (IIS, Apache, nginx), Content Delivery Networks (Fastly, Cloudfront, Cloudflare), and even URL Shorteners (bit.ly, amzn.to). Generally speaking, a URL's lengths should be kept to below 2,000 characters, and so they cannot handle a lot of data.

## Login Forms
The example below is different from the one we saw earlier since this one utilizes a login form instead of HTTP basic auth.  
<img width="827" height="275" alt="Pasted image 20250905044224" src="https://github.com/user-attachments/assets/4c477551-dcba-4594-a464-c6e449ecc3f3" />  
After logging in with admin:admin we see a search function. When we use devtools we can see that a POST request was sent when we logged in.  
<img width="1276" height="478" alt="Pasted image 20250905044714" src="https://github.com/user-attachments/assets/27751be7-fcb9-4455-9962-971b55f29520" />  
The data in the POST request: `username=admin&password=admin`. To try and replicate this request with `curl` we need to use `-X POST` then to add the data use `-d` and place the data after it.  
```shell-session
AnasSalhen@htb[/htb]$ curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/

<SNIP>
        <em>Type a city name and hit <strong>Enter</strong></em>
<SNIP>
```
We can see the text "Type a city name..." which means we logged in successfully and now are seeing the search page not the login page.

## Authenticated Cookies
If we were successfully authenticated, we should have received a cookie so our browsers can persist our authentication, and we don't need to login every time we visit the page. We can use the `-v` or `-i` flags to view the response, which should contain the `Set-Cookie` header with our authenticated cookie:
```shell-session
AnasSalhen@htb[/htb]$ curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/ -i

HTTP/1.1 200 OK
Date: 
Server: Apache/2.4.41 (Ubuntu)
Set-Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1; path=/
<SNIP>
```

Now that we got our cookie we need to include it when we interact with this web application to stay authenticated with the `-b` flag:  
`curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/`  
or just with the `-H` flag: `curl -H 'Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/`

If we go back to the browser and instead of logging in and providing credentials we open the Storage tab in devtools and we replace the existing `PHPSESSID` cookie if there is one. If not we create a new one with the appropriate name and value. The cookie name is the one before the equal sign (`PHPSESSID`) and the value is after the equal sign (`c1nsa6op7vtk7kdis7bcnbadf1`).  
<img width="1274" height="456" alt="Pasted image 20250905051406" src="https://github.com/user-attachments/assets/90cfe629-69f6-42af-8399-c8b42d3643ae" />  

See how a valid cookie is enough to get authenticated in many web apps? This is useful for many attacks like XSS.

## JSON Data
Let's examine the request that is sent when we search for a city (i.e. london).  
<img width="1278" height="477" alt="Pasted image 20250905051702" src="https://github.com/user-attachments/assets/fdc6831a-2889-430c-8f01-d3be053bf2ab" />  
As we can see, the search form sends a POST request to `search.php`, with the following data:
```json
{"search":"london"}
```
The POST data appear to be in JSON format, so our request must have specified the `Content-Type` header to be `application/json`. So, to replicate a request like this we need to use:
```shell-session
AnasSalhen@htb[/htb]$ curl -X POST -d '{"search":"london"}' -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' -H 'Content-Type: application/json' http://<SERVER_IP>:<PORT>/search.php
["London (UK)"]
```

# CRUD API
There are several types of APIs that serve different purposes. For example we might have an API endpoint called `api.php` which manages a database. If we wanted to update the "city" table in the database, and the row we will be updating has a city name of "london", then the URL would look something like this:
```bash
curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london <SNIP>
```

## CRUD
In general CRUD APIs do 4 main functions:

|Operation|HTTP Method|Description|
|---|---|---|
|`Create`|`POST`|Adds the specified data to the database table|
|`Read`|`GET`|Reads the specified entity from the database table|
|`Update`|`PUT`|Updates the data of the specified database table|
|`Delete`|`DELETE`|Removes the specified row from the database table|

## Read
```shell-session
AnasSalhen@htb[/htb]$ curl http://<SERVER_IP>:<PORT>/api.php/city/london

[{"city_name":"London","country_name":"(UK)"}]
```
The output we received is a JSON string. To have it properly formatted pipe it to `jq`. We will also silent any unneeded cURL output with `-s`.
```shell-session
AnasSalhen@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq

[
  {
    "city_name": "London",
    "country_name": "(UK)"
  }
]
```
We can also provide a search term and get all matching results:
```shell-session
AnasSalhen@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/le | jq

[
  {
    "city_name": "Leeds",
    "country_name": "(UK)"
  },
  {
    "city_name": "Dudley",
    "country_name": "(UK)"
  },
  {
    "city_name": "Leicester",
    "country_name": "(UK)"
  },
  <SNIP>
]
```
We can also leave the string empty, instead of `london` or `le` we will use `http://<SERVER_IP>:<PORT>/api.php/city/` which will output all entries in the table.

## Create
We can add a new JSON entry to the table using something like: 
```shell-session
AnasSalhen@htb[/htb]$ curl -X POST http://<SERVER_IP>:<PORT>/api.php/city/ -d '{"city_name":"HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```
Check for it:
```shell-session
AnasSalhen@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/HTB_City | jq

[
  {
    "city_name": "HTB_City",
    "country_name": "HTB"
  }
]
```

## Update
When using `PUT` we have to specify the name of the entry we want to edit in the URL. For example to edit the entry for `london` use:
```shell-session
AnasSalhen@htb[/htb]$ curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london -d '{"city_name":"New_HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```
In the above example, even if an entry with a `london` city did not exist, it would create a new entry with the details we passed.

The HTTP `PATCH` method may also be used to update API entries instead of `PUT`. To be precise, `PATCH` is used to partially update an entry (only modify some of its data "e.g. only city_name"), while `PUT` is used to update the entire entry.

## Delete
```shell-session
AnasSalhen@htb[/htb]$ curl -X DELETE http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City
```
