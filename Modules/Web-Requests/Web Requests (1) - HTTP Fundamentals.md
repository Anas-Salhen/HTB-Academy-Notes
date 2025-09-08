# HyperText Transfer Protocol (HTTP)
We enter a Fully Qualified Domain Name (FQDN) as a Uniform Resource Locator (URL) to reach the desired website. The structure of a URL:  
<img width="1940" height="480" alt="Pasted image 20250904050344" src="https://github.com/user-attachments/assets/f9f53d66-07ae-421c-894d-922750e1f107" />  
- Scheme: used to identify the protocol being accessed by the client.
- User: this is an optional component that contains the credentials (separated by a colon ":") used to authenticate to the host, and is separated from the host with an at sign (@).
- Host: this can be a hostname or an IP address.
- Port: separated from the Host by a colon (:). If no port is specified, http schemes default to port 80 and https default to port 443.
- Path: points to the resource being accessed, which can be a file or a folder. If there is no path specified, the server returns the default index (e.g. `index.html`).
- Query string: starts with a question mark (?), and consists of a parameter (e.g. login) and a value (e.g. true). Multiple parameters can be separated by an ampersand (&).
- Fragments: they are processed by the browsers on the client-side to locate sections within the primary resource (e.g. a header or section on the page).

## HTTP Flow
<img width="2280" height="1041" alt="Pasted image 20250904051054" src="https://github.com/user-attachments/assets/64b2a29e-069b-472a-a6ac-b723569487c2" />  
- User enters domain name to the browser (inlanefreight.com).
- The browser sends a request to a DNS server to get the IP address for that domain.
- DNS server sends it the IP address.
- User starts communicating with the website using the IP address

Our browsers usually first look up records in the local '`/etc/hosts`' file, and if the requested domain does not exist within it, then they would contact other DNS servers. We can use the '`/etc/hosts`' to manually add records for DNS resolution, by adding the IP followed by the domain name.

Once the browser gets the IP address, it sends a GET request to the default HTTP port (e.g. `80`), asking for the root `/` path. Then, the web server receives the request and processes it. By default, servers are configured to return an index file when a request for `/` is received.

In this case, the contents of `index.html` are read and returned by the web server as an HTTP response. The response also contains the status code (e.g. `200 OK`), which indicates that the request was successfully processed. The web browser then renders the `index.html` contents and presents it to the user.

## cURL
`curl` (client URL) is a command-line tool that supports HTTP among other protocols. Example: 
```shell-session
AnasSalhen@htb[/htb]$ curl inlanefreight.com

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```
It didn't render the page content like a web browser does, still this info in this format is valuable. We can also download a page or a file and save its content to a file on our system using the `-O` flag.

# Hypertext Transfer Protocol Secure (HTTPS)
One of the significant drawbacks of HTTP is that all data is transferred in clear-text. To fix this HTTPS was created which provides encryption for all communications. Even though the data itself is encrypted someone analyzing the packets would know the site you are visiting from the destination IP of the packet or from DNS messages. To prevent this use an encrypted DNS server and VPN.

When using `curl` it should automatically handle all HTTPS communication standards and perform a secure handshake and then encrypt and decrypt data automatically. However, if we ever contact a website with an invalid SSL certificate or an outdated one, then `curl` by default would not proceed with the communication to protect against MITM attacks:
```shell-session
AnasSalhen@htb[/htb]$ curl https://inlanefreight.com

curl: (60) SSL certificate problem: Invalid certificate chain
More details here: https://curl.haxx.se/docs/sslcerts.html
...SNIP...
```
Modern web browsers also warn the user when there is an invalid SSL certificate. If we choose to skip the certificate check with `curl`, we can use the `-k` flag.

# HTTP Requests and Responses
## HTTP Request
<img width="1981" height="961" alt="Pasted image 20250904214942" src="https://github.com/user-attachments/assets/0d9a1a8f-0914-4672-a10a-906091e2d48c" />  
The first line of any HTTP request contains three main fields separated by spaces:
1. Method (GET): type of action to perform.
2. Path (/users/login.html): path to the resource being accessed. This field can also be suffixed with a query string (e.g. ?username=user).
3. Version (HTTP/1.1)
The next set of lines contain HTTP header value pairs, like `Host`, `User-Agent`, `Cookie`, and many others.

## HTTP Response
<img width="2020" height="1127" alt="Pasted image 20250904215250" src="https://github.com/user-attachments/assets/cb618182-c540-4212-ac0b-f1fecfa0fdc9" />  
After the response headers there may be a response body which is usually HTML but could be JSON, an image or even a document.

## curl and Devtools
Earlier with `curl` we got only the response body. If we want to see the full HTTP request and the full HTTP response with headers and everything we should use the `-v` flag and it will show us both. The `-I` flag sends a `HEAD` request which shows only the response headers, not the request headers nor response body. The `-i` flag shows the response headers and response body.

Many browsers come with devtools to help developers test their web apps. As penetration testers these tools can be useful for us but for other purposes. To open the browser devtools in either Chrome or Firefox, we can click `CTRL+SHIFT+I` or simply click `F12`.

# HTTP Headers
Headers can be divided into these categories:
1. General Headers
2. Entity Headers
3. Request Headers
4. Response Headers
5. Security Headers

## General Headers
Used in both HTTP requests and responses, they are contextual and are used to describe the message rather than its contents.

| **Header**   | **Example**                           | **Description**                                                                                                                                                                                                                                                                                                                                                                           |
| ------------ | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Date`       | `Date: Wed, 16 Feb 2022 10:38:44 GMT` | Holds the date and time at which the message originated. It's preferred to convert the time to the standard [UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) time zone.                                                                                                                                                                                                    |
| `Connection` | `Connection: close`                   | Dictates if the current network connection should stay alive after the request finishes. Two commonly used values for this header are `close` and `keep-alive`. The `close` value from either the client or server means that they would like to terminate the connection, while the `keep-alive` header indicates that the connection should remain open to receive more data and input. |

## Entity Headers
Also common to both the request and response. These headers are used to describe the content (entity) transferred by a message. They are usually found in responses and POST or PUT requests.

| **Header**         | **Example**                   | **Description**                                                                                                                                                                                                                                                            |
| ------------------ | ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Content-Type`     | `Content-Type: text/html`     | Used to describe the type of resource being transferred. The value is automatically added by the browsers on the client-side and returned in the server response. The `charset` field denotes the encoding standard, such as [UTF-8](https://en.wikipedia.org/wiki/UTF-8). |
| `Media-Type`       | `Media-Type: application/pdf` | The `media-type` is similar to `Content-Type`, and describes the data being transferred. This header can play a crucial role in making the server interpret our input. The `charset` field may also be used with this header.                                              |
| `Boundary`         | `boundary="b4e4fbd93540"`     | Acts as a marker to separate content when there is more than one in the same message. For example, within a form data, this boundary gets used as `--b4e4fbd93540` to separate different parts of the form.                                                                |
| `Content-Length`   | `Content-Length: 385`         | Holds the size of the entity being passed. This header is necessary as the server uses it to read data from the message body, and is automatically generated by the browser and tools like cURL.                                                                           |
| `Content-Encoding` | `Content-Encoding: gzip`      | Data can undergo multiple transformations before being passed. For example, large amounts of data can be compressed to reduce the message size. The type of encoding being used should be specified using the `Content-Encoding` header.                                   |

## Request Headers
| **Header**      | **Example**                              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| --------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Host`          | `Host: www.inlanefreight.com`            | Used to specify the host being queried for the resource. This can be a domain name or an IP address. HTTP servers can be configured to host different websites, which are revealed based on the hostname. This makes the host header an important enumeration target, as it can indicate the existence of other hosts on the target server.                                                                                                                          |
| `User-Agent`    | `User-Agent: curl/7.77.0`                | The `User-Agent` header is used to describe the client requesting resources. This header can reveal a lot about the client, such as the browser, its version, and the operating system.                                                                                                                                                                                                                                                                              |
| `Referer`       | `Referer: http://www.inlanefreight.com/` | Denotes where the current request is coming from. For example, clicking a link from Google search results would make `https://google.com` the referer. Trusting this header can be dangerous as it can be easily manipulated, leading to unintended consequences.                                                                                                                                                                                                    |
| `Accept`        | `Accept: */*`                            | The `Accept` header describes which media types the client can understand. It can contain multiple media types separated by commas. The `*/*` value signifies that all media types are accepted.                                                                                                                                                                                                                                                                     |
| `Cookie`        | `Cookie: PHPSESSID=b4e4fbd93540`         | Contains cookie-value pairs in the format `name=value`. A [cookie](https://en.wikipedia.org/wiki/HTTP_cookie) is a piece of data stored on the client-side and on the server, which acts as an identifier. These are passed to the server per request, thus maintaining the client's access. Cookies can also serve other purposes, such as saving user preferences or session tracking. There can be multiple cookies in a single header separated by a semi-colon. |
| `Authorization` | `Authorization: BASIC cGFzc3dvcmQK`      | Another method for the server to identify clients. After successful authentication, the server returns a token unique to the client. Unlike cookies, tokens are stored only on the client-side and retrieved by the server per request. There are multiple types of authentication types based on the webserver and application type used.                                                                                                                           |

## Response Headers
| **Header**         | **Example**                                 | **Description**                                                                                                                                                                           |
| ------------------ | ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Server`           | `Server: Apache/2.2.14 (Win32)`             | Contains information about the HTTP server, which processed the request. It can be used to gain information about the server, such as its version, and enumerate it further.              |
| `Set-Cookie`       | `Set-Cookie: PHPSESSID=b4e4fbd93540`        | Contains the cookies needed for client identification. Browsers parse the cookies and store them for future requests. This header follows the same format as the `Cookie` request header. |
| `WWW-Authenticate` | `WWW-Authenticate: BASIC realm="localhost"` | Notifies the client about the type of authentication required to access the requested resource.                                                                                           |

## Security Headers
| **Header**                  | **Example**                                   | **Description**                                                                                                                                                                                                                                                                                                                             |
| --------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Content-Security-Policy`   | `Content-Security-Policy: script-src 'self'`  | Dictates the website's policy towards externally injected resources. This could be JavaScript code as well as script resources. This header instructs the browser to accept resources only from certain trusted domains, hence preventing attacks such as [Cross-site scripting (XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting). |
| `Strict-Transport-Security` | `Strict-Transport-Security: max-age=31536000` | Prevents the browser from accessing the website over the plaintext HTTP protocol, and forces all communication to be carried over the secure HTTPS protocol. This prevents attackers from sniffing web traffic and accessing protected information such as passwords or other sensitive data.                                               |
| `Referrer-Policy`           | `Referrer-Policy: origin`                     | Dictates whether the browser should include the value specified via the `Referer` header or not. It can help in avoiding disclosing sensitive URLs and information while browsing the website.                                                                                                                                              |
A complete list of standard HTTP headers can be found [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers).

## curl and Devtools
Using this flag `-H` in `curl` allows us to set the request headers ourselves. Some headers like `User-Agent` or `Cookie` have their own flags like `-A` for setting `User-Agent`.
```shell-session
AnasSalhen@htb[/htb]$ curl https://www.inlanefreight.com -A 'Mozilla/5.0'

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

We can view headers for these requests using devtools too.  
<img width="1277" height="527" alt="Pasted image 20250904230942" src="https://github.com/user-attachments/assets/634b1c9c-6ddf-4bf3-b810-38ffbbea46e7" />  
From the Network tab we can see the request and response with their body and headers.
