# Common Web Vulnerabilities
## Broken Authentication/Access Control
Broken Authentication refers to vulnerabilities that allow attackers to bypass authentication functions. For example, this may allow an attacker to login without having a valid set of credentials or allow a normal user to become an administrator without having the privileges to do so.

Broken Access Control refers to vulnerabilities that allow attackers to access pages and features they should not have access to. For example, a normal user gaining access to the admin panel.

## Malicious File Upload
If the web application has a file upload feature and does not properly validate the uploaded files, we may upload a malicious script (i.e., a PHP script), which will allow us to execute commands on the remote server.

## Command Injection
Many applications use user input to execute certain commands. If not validated properly, attackers can enter commands instead of normal input.

## SQL Injection (SQLi)
Similarly to a Command Injection vulnerability, this vulnerability may occur when the web application executes a SQL query, including a value taken from user-supplied input. This may allow us to control the database or view data we are not supposed to view.

# Public Vulnerabilities
## Public CVE
As many organizations deploy web applications that are publicly used these web applications tend to be tested by many organizations and experts around the world. This leads to frequently uncovering a large number of vulnerabilities, most of which get patched and then shared publicly and assigned a CVE ([Common Vulnerabilities and Exposures](https://en.wikipedia.org/wiki/Common_Vulnerabilities_and_Exposures)) record and score.

There are many available exploits online made for testing and educational purposes. Before searching them we need to get the version of the web app we will test. Then we search google and exploit databases like [Exploit DB](https://www.exploit-db.com/), [Rapid7 DB](https://www.rapid7.com/db/), or [Vulnerability Lab](https://www.vulnerability-lab.com/) for exploits for that version.

We would usually be interested in exploits with a CVE score of 8-10 or exploits that lead to Remote Code Execution (RCE). Furthermore, if a web application uses external components (e.g., a plugin), we should also search for vulnerabilities for these external components.

## Common Vulnerability Scoring System (CVSS)
CVSS is an open-source industry standard for assessing the severity of security vulnerabilities. CVSS scores depend on three metrics Base, Temporal and Environmental. When calculating severity the Base metrics produce a score ranging from 0 to 10, modified by applying Temporal and Environmental metrics. 

The [National Vulnerability Database (NVD)](https://nvd.nist.gov/) provides CVSS scores for almost all known, publicly disclosed vulnerabilities. It only provides Base scores without accounting for Temporal and Environmental metrics. Temporal metrics change over time and Environmental is unique to each organization.

The NVD provides a [CVSS v2 calculator](https://nvd.nist.gov/vuln-metrics/cvss/v2-calculator) and a [CVSS v3 calculator](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator) that organizations can use to calculate accurate severity that factors in Temporal and Environmental data unique to them. Scoring ratings slightly differ between V2 and V3:

CVSS V2.0 Ratings:
- Low: 0.0-3.9
- Medium: 4.0-6.9
- High: 7.0-10.0

CVSS V3.0 Ratings:
- None: 0.0
- Low: 0.1-3.9
- Medium: 4.0-6.9
- High: 7.0-8.9
- Critical: 9.0-10.0

## Back-end Server Vulnerabilities
Like public vulnerabilities for web applications, we should also consider looking for vulnerabilities for other back end components, like the back end server or the web server.

The most critical vulnerabilities for back-end components are found in web servers, as they are publicly accessible over the `TCP` protocol. An example of a well-known web server vulnerability is the `Shell-Shock`, which affected Apache web servers released during and before 2014 and utilized `HTTP` requests to gain remote control over the back-end server.
