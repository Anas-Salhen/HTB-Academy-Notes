# Intro
## Web Applications vs. Websites
In the past, we interacted with websites that are static and cannot be changed in real-time. These types of static pages do not contain functions and, therefore, do not produce real-time changes. That type of website is also known as [Web 1.0](https://en.wikipedia.org/wiki/Web_2.0#Web_1.0).  
<img width="2364" height="1081" alt="Pasted image 20250905145817" src="https://github.com/user-attachments/assets/fca8402c-0f80-44a0-b644-fc634776331d" />  
On the other hand, most websites run web applications, or [Web 2.0](https://en.wikipedia.org/wiki/Web_2.0) presenting dynamic content based on user interaction.

## Web Applications vs. Native Operating System Applications
Web apps advantages:
- Unlike native operating system (native OS) applications, web applications are platform-independent and can run in a browser on any operating system.
- All users accessing a web application use the same version and the same web application. When there is an update it gets installed in a single location (webserver) without developing different builds for each platform.

Native OS advantages:
- Operation speed and the ability to utilize native operating system libraries and local hardware.
- Usually more capable than web applications, as they have a deeper integration with the operating system and are not limited to the browser's capabilities only.

## Web Application Distribution
There are many open-source web applications used by organizations worldwide that can be customized to meet each organization's needs. Some common open source web applications include:
- [WordPress](https://wordpress.com/)
- [OpenCart](https://www.opencart.com/)
- [Joomla](https://www.joomla.org/)

There are also proprietary 'closed source' web applications, which are usually developed by a certain organization and then sold to another organization or used by organizations through a subscription plan model.
- [Wix](https://www.wix.com/)
- [Shopify](https://www.shopify.com/)
- [DotNetNuke](https://www.dnnsoftware.com/)

## Security Risks of Web Applications
One of the most current and widely used methods for testing web applications is the [OWASP Web Security Testing Guide](https://github.com/OWASP/wstg/tree/master/document/4-Web_Application_Security_Testing). 

One of the most common procedures is to start by reviewing a web application's front end components, such as `HTML`, `CSS` and `JavaScript` and attempt to find vulnerabilities such as [Sensitive Data Exposure](https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure) and [Cross-Site Scripting (XSS)](https://owasp.org/www-project-top-ten/2017/A7_2017-Cross-Site_Scripting_\(XSS\)). Once all front end components are thoroughly tested, we would typically review the web application's core functionality and the interaction between the browser and the webserver.

We typically assess web applications from both an unauthenticated and authenticated perspective (if the application has login functionality) to maximize coverage and review every possible attack scenario.

## Attacking Web Applications
Web attacks can deal significant damage to organizations especially when they are chained together they can be very dangerous. Consider the following examples:

|Flaw|Real-world Scenario|
|---|---|
|[SQL injection](https://owasp.org/www-community/attacks/SQL_Injection)|Obtaining Active Directory usernames and performing a password spraying attack against a VPN or email portal.|
|[File Inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion)|Reading source code to find a hidden page or directory which exposes additional functionality that can be used to gain remote code execution.|
|[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)|A web application that allows a user to upload a profile picture that allows any file type to be uploaded (not just images). This can be leveraged to gain full control of the web application server by uploading malicious code.|
|[Insecure Direct Object Referencing (IDOR)](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)|When combined with a flaw such as broken access control, this can often be used to access another user's files or functionality. An example would be editing your user profile browsing to a page such as /user/701/edit-profile. If we can change the `701` to `702`, we may edit another user's profile!|
|[Broken Access Control](https://owasp.org/www-project-top-ten/2017/A5_2017-Broken_Access_Control)|Another example is an application that allows a user to register a new account. If the account registration functionality is designed poorly, a user may perform privilege escalation when registering. Consider the `POST` request when registering a new user, which submits the data `username=bjones&password=Welcome1&email=bjones@inlanefreight.local&roleid=3`. What if we can manipulate the `roleid` parameter and change it to `0` or `1`. We have seen real-world applications where this was the case, and it was possible to quickly register an admin user and access many unintended features of the web application.|

# Web Application Layout
Web application layouts consist of many different layers that can be summarized with the following three main categories:

| **Category**                     | **Description**                                                                                                                                                                                                                                                      |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Web Application Infrastructure` | Describes the structure of required components, such as the database, needed for the web application to function as intended. Since the web application can be set up to run on a separate server, it is essential to know which database server it needs to access. |
| `Web Application Components`     | The components that make up a web application represent all the components that the web application interacts with. These are divided into the following three areas: `UI/UX`, `Client`, and `Server` components.                                                    |
| `Web Application Architecture`   | Architecture comprises all the relationships between the various web application components.                                                                                                                                                                         |

## Web Application Infrastructure
The most common infrastructure setups (models) are:
- Client-Server
- One Server
- Many Servers - One Database
- Many Servers - Many Databases
Aside from these models, there are other web application models available such as [serverless](https://aws.amazon.com/lambda/serverless-architectures-learn-more) web applications or web applications that utilize [microservices](https://aws.amazon.com/microservices).
### Client-Server
A server hosts the web application in a client-server model and distributes it to any clients trying to access it.  
<img width="1503" height="626" alt="Pasted image 20250905194642" src="https://github.com/user-attachments/assets/497c765d-7a38-43b1-929b-8f9ab84402bd" />  

### One Server
In this architecture, the entire web application or even several web applications and their components, including the database, are hosted on a single server.  
<img width="1451" height="820" alt="Pasted image 20250905195322" src="https://github.com/user-attachments/assets/0b99a433-fad8-47ba-aed4-08d0e1255e44" />  
This is the riskiest design, if any web application hosted on this server is compromised in this architecture, then all web applications' data will be compromised. This design represents an "`all eggs in one basket`" approach.

### Many Servers - One Database
This model separates the database onto its own database server and allows the web applications' hosting server to access the database server to store and retrieve data. It can be seen as many-servers to one-database and one-server to one-database, as long as the database is separated on its own database server.  
<img width="1992" height="1245" alt="Pasted image 20250905195522" src="https://github.com/user-attachments/assets/1492613f-2b45-4702-920b-cd1ee85e3537" />  
This model can allow several web applications to access a single database to have access to the same data without syncing the data between them.

This model's main advantage (from a security point of view) is segmentation, where each of the main components of a web application is located and hosted separately. In case one server is compromised, other servers are not directly affected.

### Many Servers - Many Databases
In this model, within the database server, each web application's data is hosted in a separate database. The web application can only access private data and only common data that is shared across web applications. It is also possible to host each web application's database on its separate database server.  
<img width="1875" height="1250" alt="Pasted image 20250905195815" src="https://github.com/user-attachments/assets/e9b3e3d9-c4de-42ab-bbc8-f0a373285c8e" />  
This design is also widely used for redundancy purposes, if a server goes down its backup replaces it to reduce downtime as much as possible.

## Web Application Components
1. Client
2. Server
    - Webserver
    - Web Application Logic
    - Database
3. Services (Microservices)
    - 3rd Party Integrations
    - Web Application Integrations
4. Functions (Serverless)

## Web Application Architecture
The components of a web application are divided into three different layers.

|**Layer**|**Description**|
|---|---|
|`Presentation Layer`|Consists of UI process components that enable communication with the application and the system. These can be accessed by the client via the web browser and are returned in the form of HTML, JavaScript, and CSS.|
|`Application Layer`|This layer ensures that all client requests (web requests) are correctly processed. Various criteria are checked, such as authorization, privileges, and data passed on to the client.|
|`Data Layer`|The data layer works closely with the application layer to determine exactly where the required data is stored and can be accessed.|
An example of a web application architecture could look something like this:  
<img width="1378" height="749" alt="Pasted image 20250905200134" src="https://github.com/user-attachments/assets/c1987de6-ee6d-4f99-a689-8b372a17bb26" />  
Source: [Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)

Furthermore, some web servers can run operating system calls and programs, like [IIS ISAPI](https://learn.microsoft.com/en-us/previous-versions/iis/6.0-sdk/ms525172\(v=vs.90\)) or [PHP-CGI](https://www.php.net/manual/en/install.unix.commandline.php).

### Microservices
We can think of them as, independent components of the web application, which in most cases are programmed for one task only. Example (online store):
- Registration
- Search
- Payments
- Ratings
- Reviews
Each of these microservices communicates with the client and with each other. Their communication is stateless which means:
- Each request/response is independent.
- They don’t “remember” past interactions.
- Data is stored in a separate database/service, not inside the microservice itself.
The use of microservices is considered [service-oriented architecture (SOA)](https://en.wikipedia.org/wiki/Service-oriented_architecture), built as a collection of different automated functions focused on a single business goal.

An advantage of microservices is that they can be written in different programming languages and still interact. They offer many benefits such as:
- Agility
- Flexible scaling
- Easy deployment
- Reusable code
- Resilience

### Serverless
Cloud providers such as AWS, GCP, Azure, among others, offer serverless architectures. With this model there is no need to worry about managing servers, scaling or maintenance, it's all done by the cloud provider. Your web application will run in stateless containers (Docker, for example).

# Front End vs. Back End
## Front End
The front end of a web application contains the user's components directly through their web browser (client-side). These components make up the source code of the web page we view when visiting a web application and usually include `HTML`, `CSS`, and `JavaScript`, which is then interpreted in real-time by our browsers.

In modern web applications, front end components should adapt to any screen size and work within any browser on any device. This contrasts with back end components, which are usually written for a specific platform or operating system. If the front end of a web application is not well optimized for different devices, platforms, etc., it will cause slow and bad performance.

Aside from front-end code development, the following are some of the other tasks related to front end web application development:
- Visual Concept Web Design
- User Interface (UI) design
- User Experience (UX) design

There are many sites available to us to practice front end coding. One example is [this one](https://html-css-js.com/). Here we can play around with the [editor](https://htmlg.com/html-editor/), typing and formatting text and seeing the resulting `HTML`, `CSS`, and `JavaScript` being generated for us.

## Back End
The back end of a web application drives and executes all of the core web application functionalities. There are four main back end components for web applications:

| **Component**            | **Description**                                                                                                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Back end Servers`       | The hardware and operating system that hosts all other components and are usually run on operating systems like `Linux`, `Windows`, or using `Containers`.                                                                   |
| `Web Servers`            | Web servers handle HTTP requests and connections. Some examples are `Apache`, `NGINX`, and `IIS`.                                                                                                                            |
| `Databases`              | Databases (`DBs`) store and retrieve the web application data. Some examples of relational databases are `MySQL`, `MSSQL`, `Oracle`, `PostgreSQL`, while examples of non-relational databases include `NoSQL` and `MongoDB`. |
| `Development Frameworks` | Development Frameworks are used to develop the core Web Application. Some well-known frameworks include `Laravel` (`PHP`), `ASP.NET` (`C#`), `Spring` (`Java`), `Django` (`Python`), and `Express` (`NodeJS JavaScript`).    |
It is also possible to host each component of the back end on its own isolated server, or in isolated containers, by utilizing services such as [Docker](https://www.docker.com/). Each one could even be installed on its own server but this is resource-consuming so it depends on business needs.

Main tasks performed by back end components:
- Develop the main logic and services of the back end of the web application
- Develop and maintain the back end database
- Develop and implement libraries to be used by the web application
- Implement the main [APIs](https://en.wikipedia.org/wiki/API) for front end component communications
- Integrate remote servers and cloud services into the web application

## Securing Front/Back End
When we have full access to the source code of front end components, we can perform a code review to find vulnerabilities, which is part of what is referred to as [Whitebox Pentesting](https://en.wikipedia.org/wiki/White-box_testing).

On the other hand, back end components' source code is stored on the back end server, so we do not have access to it by default, which forces us only to perform what is known as [Blackbox Pentesting](https://en.wikipedia.org/wiki/Black-box_testing). However, some web apps are open source, meaning we likely have access to their source code. Furthermore, some vulnerabilities such as [Local File Inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion) could allow us to obtain the source code from the back end server.

The top 20 most common mistakes web developers make:
1. Permitting Invalid Data to Enter the Database
2. Focusing on the System as a Whole
3. Establishing Personally Developed Security Methods
4. Treating Security to be Your Last Step
5. Developing Plain Text Password Storage
6. Creating Weak Passwords
7. Storing Unencrypted Data in the Database
8. Depending Excessively on the Client Side
9. Being Too Optimistic
10. Permitting Variables via the URL Path Name
11. Trusting third-party code
12. Hard-coding backdoor accounts
13. Unverified SQL injections
14. Remote file inclusions
15. Insecure data handling
16. Failing to encrypt data properly
17. Not using a secure cryptographic system
18. Ignoring layer 8
19. Review user actions
20. Web Application Firewall misconfigurations

These mistakes lead to the [OWASP Top 10](https://owasp.org/www-project-top-ten/) vulnerabilities for web applications:
1. Broken Access Control
2. Cryptographic Failures
3. Injection
4. Insecure Design
5. Security Misconfiguration
6. Vulnerable and Outdated Components
7. Identification and Authentication Failures
8. Software and Data Integrity Failures
9. Security Logging and Monitoring Failures
10. Server-Side Request Forgery (SSRF)
