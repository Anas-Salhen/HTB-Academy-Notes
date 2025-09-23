# Proxy Setup
## Pre-Configured Browser
A popular option is to use pre-configured browsers that come with Burp (Proxy -> Intercept) or ZAP (click Firefox icon).

## Proxy Setup
If you use ZAP or Burp they listen by default on port 8080. So if you want to use your normal browser instead of the pre-configured one you need to tell it to use 8080 instead of 443 or 80. Firefox for example has an extension called Foxy Proxy which makes this easier.

<img width="503" height="173" alt="Pasted image 20250909143018" src="https://github.com/user-attachments/assets/62ef57cf-b3b4-41d7-9376-db522cc2a4d4" />  
Click options

<img width="2522" height="929" alt="Pasted image 20250909143118" src="https://github.com/user-attachments/assets/e0a327f8-93f6-4cec-900d-e7f5c6566169" />  
Then use 127.0.0.1 as the IP, and 8080 as the port, and name it Burp or ZAP.

So now communication happens as follows:
- Client (browser) sends packets to the proxy (source port: random high number, destination port: 8080)
- Proxy sends packets to the web server (source port: new random high number, destination port: 443/80)

## Installing CA Certificate
We can install Burp's certificate once we select Burp as our proxy in Foxy Proxy, by browsing to http://burp, and download the certificate from there by clicking on CA Certificate:  
<img width="755" height="122" alt="Pasted image 20250909233846" src="https://github.com/user-attachments/assets/4f303ba2-34f9-452e-b835-c10be22f9d39" />  

To get ZAP's certificate, we can go to (`Tools>Options>Dynamic SSL Certificate`), then click on `Save`:  
<img width="748" height="488" alt="Pasted image 20250909233929" src="https://github.com/user-attachments/assets/9a3f26fa-fcf1-4c11-a755-4c881831db48" />  
We can also change our certificate by generating a new one with the `Generate` button.

Once we have our certificates, we can install them within Firefox by browsing to [about:preferences#privacy](about:preferences#privacy), scrolling to the bottom, and clicking `View Certificates`. After that, we can select the `Authorities` tab, and then click on `import`, and select the downloaded CA certificate. Finally, we must select `Trust this CA to identify websites` and `Trust this CA to identify email users`, and then click OK.

# Intercepting Web Requests
## Burp
Navigate to the `Proxy` tab, and request interception should be on by default. When interception is on, we can start up the pre-configured browser and then visit our target. Then, once we go back to Burp, we will see the intercepted request awaiting our action, and we can click on forward to forward the request.

## ZAP
In ZAP, interception is off by default, as shown by the green button on the top bar (green indicates that requests can pass and not be intercepted). We can click on this button to turn the Request Interception on or off, or we can use the shortcut (`CTRL+B`).  
<img width="734" height="59" alt="Pasted image 20250909234604" src="https://github.com/user-attachments/assets/7070ca73-dc98-4a07-8785-27c51184daea" />  
Through the Quick Start tab click Manual Explore and choose which browser do you want to use and launch it. ZAP will remember this choice and provide a button to launch this browser in the top bar.

ZAP also has a powerful feature called `Heads Up Display (HUD)`, which allows us to control most of the main ZAP features from right within the pre-configured browser. Enable it by clicking:  
<img width="920" height="59" alt="Pasted image 20250910151934" src="https://github.com/user-attachments/assets/a6ed5f4a-c484-424d-a289-72385a8774e9" />  

One of the features of the HUD is intercepting requests, we can click on the second button from the top on the left pane to turn it on:  
<img width="2560" height="846" alt="Pasted image 20250910160120" src="https://github.com/user-attachments/assets/640bb13b-90fb-432a-96c7-52d04a186e81" />  
Now once a request is sent it will be presented to us for action.  
<img width="2560" height="1016" alt="Pasted image 20250910160342" src="https://github.com/user-attachments/assets/eb472d45-6689-4e08-acbc-f5da2f09a3fa" />  
We can choose `step` to send the request and examine its response and break any further requests. Or choose `continue` to let the page send the remaining requests.

# Intercepting Responses
Sometimes we might need to intercept responses and edit them to try and reveal anything hidden or allow ourselves to perform some restricted actions. In this example we will modify the response so that the web page accepts any value as input not only numbers.

## Burp
`Proxy>Options` then `Intercept Response` under `Intercept Server Responses`. Now refresh and we should be able to see the intercepted response:  
<img width="1012" height="183" alt="Pasted image 20250910161734" src="https://github.com/user-attachments/assets/c73a21ff-5b04-4d9d-b9d7-b6d36978d3ab" />  
Change `type="number"` on line 27 to `type="text"` and change `maxlength="3"` to `maxlength="100"` so we can enter longer input.

Now when we examine the response it will look like this:  
<img width="1938" height="435" alt="Pasted image 20250910161928" src="https://github.com/user-attachments/assets/5d01b923-7390-4dc2-903b-944392851aee" />  
We can enter the payload we want.

## ZAP
When our request is intercepted click `step` and you will intercept the response. Make the same changes that we did earlier and click `continue`, you will see the same result.

If all we wanted was to enable disabled input fields or show hidden input fields ZAP HUD has a powerful feature to do that. To enable it click on the third button on the left (light bulb):  
<img width="2560" height="896" alt="Pasted image 20250914030208" src="https://github.com/user-attachments/assets/14b3b64c-8939-42cf-8089-0fbded983d83" />  
Another useful feature is the `Comments` button, which will indicate the positions where there are HTML comments that are usually only visible in the source code. We can click on the `+` button on the left pane and select `Comments` to add the `Comments` button, and once we click on it, the `Comments` indicators should be shown.  
<img width="2560" height="871" alt="Pasted image 20250914030357" src="https://github.com/user-attachments/assets/6603c750-9c28-44ac-8eff-e1db29f5256e" />  

`Burp` also has some similar useful features like: `Proxy>Options>Response Modification>Unhide hidden form fields`.

# Automatic Modification
## Automatic Request Modification
Let's replace our `User-Agent` with `HackTheBox Agent 1.0`.

### Burp
Go to (`Proxy>Options>Match and Replace`) and click on `Add`. We will set the following options:  
<img width="567" height="256" alt="Pasted image 20250914031313" src="https://github.com/user-attachments/assets/797ed13a-ff44-472b-a6df-2407e6c49d6f" />  

| `Type`: `Request header`                      | Since the change we want to make will be in the request header and not in its body.                                                              |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Match`: `^User-Agent.*$`                     | The regex pattern that matches the entire line with `User-Agent` in it.                                                                          |
| `Replace`: `User-Agent: HackTheBox Agent 1.0` | This is the value that will replace the line we matched above.                                                                                   |
| `Regex match`: True                           | We don't know the exact User-Agent string we want to replace, so we'll use regex to match any value that matches the pattern we specified above. |

### ZAP Replacer
ZAP has a similar feature called Replacer which we can access by pressing `CTRL+R` or clicking on `Replacer` in ZAP's options menu. After clicking `Add` we apply:
<img width="534" height="277" alt="Pasted image 20250914031643" src="https://github.com/user-attachments/assets/c8e7259c-e4b3-4c48-974c-f3f730888bbf" />  

## Automatic Response Modification
Useful when we don't want to intercept the response again and edit it every time we refresh a page.

Let us go back to (`Proxy>Options>Match and Replace`) in Burp to add another rule.  
<img width="567" height="254" alt="Pasted image 20250914032520" src="https://github.com/user-attachments/assets/51eaa518-9a04-488f-a56d-35ab7ebc2989" />  
If we want more changes make more rules. The same can be done with ZAP Replacer.

# Repeating Requests
Request repeating allows us to resend any web request that has previously gone through the web proxy. This allows us to make quick changes to any request before we send it. It can be used with repetitive requests.  
To start we need to preview history.

## Burp
`Proxy>HTTP History`. Once we locate the request we want to repeat and open it, we can click `CTRL+R` in Burp to send it to the `Repeater` tab, and then we can either navigate to the `Repeater` tab or click `CTRL+SHIFT+R` to go to it directly. Once in `Repeater`, we can edit it then click on `Send` to send the request:  
<img width="1568" height="450" alt="Pasted image 20250914034839" src="https://github.com/user-attachments/assets/e4e32745-9c27-4341-8e37-6f8a48ca94bd" />  

## ZAP
In `ZAP` HUD, we can find the history of requests in the bottom `History` pane or ZAP's main UI at the bottom `History` tab as well. Once we locate our request, we can right-click on it and select `Open/Resend with Request Editor`, which would open the request editor window, and allow us to edit the request then send it with the `Send` button.  
<img width="1473" height="368" alt="Pasted image 20250914173844" src="https://github.com/user-attachments/assets/983fadc8-ac41-4db3-845a-d3db219f7fc9" />  
We can also see the `Method` drop-down menu, allowing us to quickly switch the request method to any other HTTP method.

# Encoding/Decoding
## URL Encoding
It is essential to ensure that our request data is URL-encoded and our request headers are correctly set. Otherwise, we may get a server error in the response. Some key character we may want to encode are `space`, `&` and `#`.

To URL-encode text in Burp Repeater, we can select that text and right-click on it, then select (`Convert Selection>URL>URL encode key characters`), or by selecting the text and clicking `CTRL+U`. Burp also supports URL-encoding as we type if we right-click and enable that option.

On the other hand, ZAP should automatically URL-encode all of our request data in the background before sending the request, though we may not see that explicitly.

There are other types of URL-encoding, like `Full URL-Encoding` or `Unicode URL` encoding, which may also be helpful for requests with many special characters.

## Decoding
There are types than URL encoding that we may encounter. We need to know them so that we can decode the data and see the original text. They include:
- HTML
- Unicode
- Base64
- ASCII hex

To access the full encoder in Burp, we can go to the `Decoder` tab. For example, perhaps we came across the following cookie that is base64 encoded, and we need to decode it: `eyJ1c2VybmFtZSI6Imd1ZXN0IiwgImlzX2FkbWluIjpmYWxzZX0=`

Input it in in Burp Decoder and select `Decode as > Base64`:
<img width="1274" height="349" alt="Pasted image 20250914194026" src="https://github.com/user-attachments/assets/43808108-2d21-4f48-a386-511b85584c6b" />  
The `Burp Inspector` tool which can be found in places like `Burp Proxy` and `Burp Repeater` can perform encoding/decoding.

In ZAP, we can use the `Encoder/Decoder/Hash` by clicking `CTRL+E`.

In this example the cookie holds the value `{"username":"guest", "is_admin":false}`. We may want to try and change `guest` to `admin` and `false` to `true` then encode it back using the same tools and see what happens.

# Proxying Tools
When using tools that interact with the web (like `curl`) we may want to intercept that traffic using a proxy like Burp or ZAP.

## Proxychains
This tool routes all traffic coming from any command-line tool to any proxy we specify. To use it we first have to edit `/etc/proxychains.conf`, comment out the final line and add the following line at the end of it:
```shell-session
#socks4         127.0.0.1 9050
http 127.0.0.1 8080
```
We should also enable `Quiet Mode` to reduce noise by un-commenting `quiet_mode`. Once that's done, we can prepend `proxychains` to any command, and the traffic of that command should be routed through `proxychains`.
```shell-session
AnasSalhen@htb[/htb]$ proxychains curl http://SERVER_IP:PORT

ProxyChains-3.1 (http://proxychains.sf.net)
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Ping IP</title>
    <link rel="stylesheet" href="./style.css">
</head>
...SNIP...
</html>    
```
The additional line at the beginning shows that it's being routed through `ProxyChains`. If we go back to our proxy (i.e. Burp) we should be able to see the request.

## nmap
We can use `proxychains` with `nmap` like we used it with `curl` but `nmap` has its own built-in proxy so we will try it.
```shell-session
nmap --proxies http://127.0.0.1:8080 SERVER_IP -pPORT -Pn -sC
```
In this example:
- We used the `--proxies http://127.0.0.1:8080` to specify the proxy our connection should go through. 
- Then we followed it by the IP address and port number of the target. 
- Used `-Pn` to skip host discovery (as recommended on the `man` page). 
- Then `-sC` for a basic scan.
Once again when we open our web proxy tool we see the requests made by `nmap`.

## Metasploit
To set a proxy for any exploit within Metasploit, we can use the `set PROXIES` flag.
