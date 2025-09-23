# Burp Scanner
It's a pro-only feature.

## Target Scope
To start a scan, we have the following options:
1. Start scan on a specific request from Proxy History
2. Start a new scan on a set of targets
3. Start a scan on items in-scope

To start a scan on a specific request from Proxy History, we can right-click on it once we locate it in the history, and then select `Scan` to be able to configure the scan before we run it, or select `Passive/Active Scan` to quickly start a scan with the default configurations.

There is also the Dashboard tab. If we click on new scan there it would open a configuration window to configure a scan on a set of custom targets.

If we go to (`Target>Site map`), it will show a listing of all directories and files burp has detected in various requests that went through its proxy:  
<img width="1485" height="386" alt="Pasted image 20250917043005" src="https://github.com/user-attachments/assets/184b4660-4343-48a1-b484-826247963006" />  
To add an item to our scope, we can right-click on it and select `Add to scope`. When you add the first item to your scope, Burp will give you the option to restrict its features to in-scope items only, and ignore any out-of-scope items. There is also advanced scope control that uses regex patterns to specify what to include/exclude.

## Crawler
Once we have our scope ready, we can go to the `Dashboard` tab and click on `New Scan` to configure our scan, which would be automatically populated with our in-scope items:  
<img width="1119" height="558" alt="Pasted image 20250917043740" src="https://github.com/user-attachments/assets/5ca2cabe-64e4-401e-a679-20e5548fbe2a" />  
A Web Crawler navigates a website by accessing any links found in its pages, accessing any forms, and examining any requests it makes to build a comprehensive map of the website. If we select `Crawl and Audit`, Burp will run its scanner after its Crawler (as we will see later).

Let us select `Crawl` as a start and go to the `Scan configuration`. From here, we may choose to click on `New` to build a custom configuration. For the sake of simplicity, we will click on the `Select from library` button, which gives us a few preset configurations we can pick from (or custom configurations we previously defined):  
<img width="898" height="286" alt="Pasted image 20250917044013" src="https://github.com/user-attachments/assets/8c2dbd1b-7b61-4132-b63d-30210bfd67a9" />  
We will select the `Crawl strategy - fastest`.

Then we proceed to the `application login` tab where we add a set of credentials for burp to use in any login fields it finds. This is important since crawling as an authenticated user can reveal information that is hidden from those who aren't logged in.

Another way to teach it how to login is to perform a normal login yourself in the pre-configured browser.

Then we start our scan and we can see the progress in the dashboard tab under tasks. Once the scan is done we go to the site map to view the updated map.

## Passive Scanner
Now that the site map is fully built, we may select to scan this target for potential vulnerabilities. When we choose the `Crawl and Audit` option in the `New Scan` dialog, Burp will perform two types of scans: A `Passive Vulnerability Scan` and an `Active Vulnerability Scan`.

Unlike an Active Scan, a Passive Scan does not send any new requests but analyzes the source of pages already visited in the scope and then tries to identify `potential` vulnerabilities. However, without sending any requests to test and verify these vulnerabilities, a Passive Scan can only suggest a list of potential vulnerabilities. Still, Burp Passive Scanner does provide a level of `Confidence` for each identified vulnerability, which is also helpful for prioritizing potential vulnerabilities.

To start a passive scan, we can select the target in (`Target>Site map`) or a request in Burp Proxy History, then right-click on it and select `Do passive scan` or `Passively scan this target`, its task can be seen in the `Dashboard` tab. Once the scan finishes, we can click on `View Details` to review identified vulnerabilities and then select the `Issue activity` tab:  
<img width="1764" height="189" alt="Pasted image 20250919151344" src="https://github.com/user-attachments/assets/1e9c2674-4f7a-4c34-9ab1-805d6708a2e2" />  

## Active Scanner
1. It starts by running a Crawl and a web fuzzer (like dirbuster/ffuf) to identify all possible pages
2. It runs a Passive Scan on all identified pages
3. It checks each of the identified vulnerabilities from the Passive Scan and sends requests to verify them
4. It performs a JavaScript analysis to identify further potential vulnerabilities
5. It fuzzes various identified insertion points and parameters to look for common vulnerabilities like XSS, Command Injection, SQL Injection, and other common web vulnerabilities

To start an active scan select `Do active scan` (instead of `Do passive scan` which we chose earlier) or we can start a `crawl and audit` task.

We can set the audit configurations to select what type of vulnerabilities we want to scan (defaults to all), where the scanner would attempt inserting its payloads, in addition to many other useful configurations.

## Reporting
After all scans are completed we can go to `site map` and right-click our target then select `Issue>Report issues for this host` and a report will be generated.  
<img width="974" height="409" alt="Pasted image 20250919223820" src="https://github.com/user-attachments/assets/aebea93c-b990-4dd9-b051-23421ee64216" />  

# ZAP Scanner
## Spider
This is similar to the crawler feature in burp. To start a spider scan locate a request from the history tab, right-click and select `Attack>Spider`. We can also start it through the HUD after visiting the page we want to scan.  
<img width="2560" height="966" alt="Pasted image 20250919224107" src="https://github.com/user-attachments/assets/26161ef8-ea5e-42c8-a9f7-9333e04cced6" />  
When our scan is complete, we can check the Sites tab on the main ZAP UI, or we can click on the first button on the right pane of HUD (`Sites Tree`), which should show us an expandable tree-list view of all identified websites and their sub-directories.

## Passive Scanner
As ZAP Spider runs and makes requests to various end-points, it is automatically running its passive scanner on each response. While this happens we see alerts increasing indicating issues that were found.

## Active Scanner
Once our site's tree is populated, we can click on the `Active Scan` button on the right pane to start an active scan on all identified pages.  
<img width="2560" height="966" alt="Pasted image 20250919224651" src="https://github.com/user-attachments/assets/850aa255-9ecd-4fbd-b85c-8ec1616e7fce" />  
Once the scan is done we can view the additional alerts it found, details about them and even replay them through ZAP request editor

## Reporting
Select `Report>Generate HTML Report`

# Extensions
## BApp Store
To find all available extensions, we can click on the `Extender` tab within Burp and select `BApp Store`. Some extensions worth checking out include, but are not limited to:

| .NET beautifier              | J2EEScan                  | Software Vulnerability Scanner |
| ---------------------------- | ------------------------- | ------------------------------ |
| Software Version Reporter    | Active Scan++             | Additional Scanner Checks      |
| AWS Security Checks          | Backslash Powered Scanner | Wsdler                         |
| Java Deserialization Scanner | C02                       | Cloud Storage Tester           |
| CMS Scanner                  | Error Message Checks      | Detect Dynamic JS              |
| Headers Analyzer             | HTML5 Auditor             | PHP Object Injection Check     |
| JavaScript Security          | Retire.JS                 | CSP Auditor                    |
| Random IP Address Header     | Autorize                  | CSRF Scanner                   |
| JS Link Finder               | Decoder Improved          |                                |
## ZAP Marketplace
To access ZAP's marketplace, we can click on the `Manage Add-ons` button and then select the `Marketplace` tab:  
<img width="774" height="93" alt="Pasted image 20250919233611" src="https://github.com/user-attachments/assets/ba919e9b-c0c5-4930-a0fd-625c5557c9a4" />  
From the marketplace we can get many useful extensions like `FuzzDB Files` and `FuzzDB Offensive` which add new wordlists that can be selected in ZAP's fuzzer.
