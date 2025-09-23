# Intro to Web Proxies
## What Are Web Proxies?
Web proxies are tools placed between a browser (or mobile application) and a back-end server that capture, view, and can manipulate web requests — essentially acting as man-in-the-middle (MITM) tools.
## Uses of Web Proxies
- Web application vulnerability scanning
- Web fuzzing
- Web crawling
- Web application mapping
- Web request analysis
- Web configuration testing
- Code reviews
This module will be covering the two most common web proxy tools: Burp Suite and ZAP.

## Burp Suite
[Burp Suite (Burp)](https://portswigger.net/burp) is the most common web proxy for web penetration testing. It has excellent UI and provides a built-in Chromium browser to test web applications. Some capabilities are only available to paid versions Burp Pro/Enterprise. These paid features include:
- Active web app scanner
- Fast Burp Intruder
- The ability to load certain Burp Extensions
However the community free version of Burp Suite should be enough for most pentesters. The pro features become handy once we start more advanced tests.

## OWASP Zed Attack Proxy (ZAP)
ZAP is another common web proxy tool for web penetration testing. It is a free and open-source project initiated by the [Open Web Application Security Project (OWASP)](https://owasp.org/) and maintained by the community, so no paid versions.

# Setting Up
## Burp Suite
Once we open it, if we are using the community version (free version) we will only be able to start a temporary project, temporary projects don't save progress. If we use the pro/enterprise version we would have the option to open an existing project that we saved or create a new one. Saving progress becomes useful when pentesting huge web apps or doing Active Web Scans.

For now we don't need it, so select temporary project then we will be prompted to either use Burp Default Configurations, or to Load a Configuration File.

## ZAP
When we open ZAP we will be asked  
<img width="516" height="192" alt="Pasted image 20250909020130" src="https://github.com/user-attachments/assets/11ec4db7-a26a-455b-a60f-01adbe11edf3" />  
To start a temporary project choose no.
