# Sensitive Data Exposure
[Sensitive Data Exposure](https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure) refers to the availability of sensitive data in clear-text to the end-user. This is usually found in the source code (HTML code) of the web page. This code can be viewed by right-clicking on the page and selecting View Page Source. If we can't right-click for some reason use `CTRL + U` or Burp Suite to view source code.

Sometimes we may find login credentials, hashes, exposed links or directories, or even exposed user information so we need to view the source code and see if we can identify any 'low-hanging fruit'.

Ideally, the front end source code should only contain the code necessary to run all of the web applications functions, without any extra code or comments that are not necessary for the web application to function properly. Furthermore, front end developers may want to use JavaScript code packing or obfuscation to reduce the chances of exposing sensitive data through JavaScript code.

# HTML Injection
<img width="832" height="151" alt="Pasted image 20250906182947" src="https://github.com/user-attachments/assets/2fb9d8c2-376d-48a1-b0c8-42c79ea1c0a0" />  
Let's inspect the code for this example:  
```html
<!DOCTYPE html>
<html>

<body>
    <button onclick="inputFunction()">Click to enter your name</button>
    <p id="output"></p>

    <script>
        function inputFunction() {
            var input = prompt("Please enter your name", "");

            if (input != null) {
                document.getElementById("output").innerHTML = "Your name is " + input;
            }
        }
    </script>
</body>

</html>
```
When we click the button it will prompt us to enter a name. Here, if we provide HTML code instead of a name it will be displayed on the page since input isn't validated or sanitized. Code to enter: 
```html
<style> body { background-image: url('https://academy.hackthebox.com/images/logo.svg'); } </style>
```
Result:  
<img width="1035" height="163" alt="Pasted image 20250906183423" src="https://github.com/user-attachments/assets/59a97524-2902-434f-9c5c-7ef08856d4fb" />  
Instead of displaying an image we can insert malicious ads or show a fake login form to grab user credentials.

# Cross-Site Scripting (XSS)
XSS is very similar to HTML Injection in practice. However, XSS involves the injection of JavaScript code to perform more advanced attacks on the client-side, instead of merely injecting HTML code. There are three main types of XSS:

|Type|Description|
|---|---|
|`Reflected XSS`|Occurs when user input is displayed on the page after processing (e.g., search result or error message).|
|`Stored XSS`|Occurs when user input is stored in the back end database and then displayed upon retrieval (e.g., posts or comments).|
|`DOM XSS`|Occurs when user input is directly shown in the browser and is written to an `HTML` DOM object (e.g., vulnerable username or page title).|
In the example earlier instead of that HTML code we could input this JavaScript code:
```javascript
#"><img src=/ onerror=alert(document.cookie)>
```
What it will do is that it will display a popup with the cookie value in it:  
<img width="809" height="113" alt="Pasted image 20250906191532" src="https://github.com/user-attachments/assets/dcd5ef40-d6c1-40f8-a81a-11dee9fe97ec" />  

# Cross-Site Request Forgery (CSRF)
[Cross-Site Request Forgery (CSRF)](https://owasp.org/www-community/attacks/csrf) attacks revolve around the idea of allowing the attacker to act as an authenticated user. This attack can be performed alone but we can also use XSS to do it.

## Prevention
| Type         | Description                                                                                                 |
| ------------ | ----------------------------------------------------------------------------------------------------------- |
| Sanitization | Removing special characters and non-standard characters from user input before displaying it or storing it. |
| Validation   | Ensuring that submitted user input matches the expected format (i.e., submitted email matched email format) |
These measures prevent attacks like HTML injection and XSS (which might lead to CSRF). Another solution would be to implement a [web application firewall (WAF)](https://en.wikipedia.org/wiki/Web_application_firewall), which can help prevent injection attempts automatically. Sometimes WAF solutions can be bypassed so don't ignore coding best practices.

In the case of CSRF, most modern web applications include anti-CSRF mechanisms, such as requiring a unique token for each session or request. Additionally, HTTP-level defenses like the `SameSite` cookie attribute (`SameSite=Strict` or `Lax`) can restrict browsers from including authentication cookies in cross-origin requests.

These measures help reduce XSS and CSRF attacks but they can still be bypassed in some scenarios.
