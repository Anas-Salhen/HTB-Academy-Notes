# Automating Recon
## Why
- `Efficiency`.
- `Scalability`.
- `Consistency`.
- `Comprehensive Coverage`: Automation can be programmed to perform a wide range of reconnaissance tasks.
- `Integration`: Many automation frameworks allow for easy integration with other tools and platforms.

## Frameworks
- [FinalRecon](https://github.com/thewhiteh4t/FinalRecon): A Python-based reconnaissance tool offering a range of modules for different tasks like SSL certificate checking, Whois information gathering, header analysis, and crawling. Its modular structure enables easy customisation for specific needs.
- [Recon-ng](https://github.com/lanmaster53/recon-ng): A powerful framework written in Python that offers a modular structure with various modules for different reconnaissance tasks. It can perform DNS enumeration, subdomain discovery, port scanning, web crawling, and even exploit known vulnerabilities.
- [theHarvester](https://github.com/laramies/theHarvester): Specifically designed for gathering email addresses, subdomains, hosts, employee names, open ports, and banners from different public sources like search engines, PGP key servers, and the SHODAN database. It is a command-line tool written in Python.
- [SpiderFoot](https://github.com/smicallef/spiderfoot): An open-source intelligence automation tool that integrates with various data sources to collect information about a target, including IP addresses, domain names, email addresses, and social media profiles. It can perform DNS lookups, web crawling, port scanning, and more.
- [OSINT Framework](https://osintframework.com/): A collection of various tools and resources for open-source intelligence gathering. It covers a wide range of information sources, including social media, search engines, public records, and more.

## FinalRecon
It offers a lot of info including:
- `Header Information`: Reveals server details, technologies used, and potential security misconfigurations.
- `Whois Lookup`: Uncovers domain registration details, including registrant information and contact details.
- `SSL Certificate Information`: Examines the SSL/TLS certificate for validity, issuer, and other relevant details.
- `Crawler`:
    - HTML, CSS, JavaScript: Extracts links, resources, and potential vulnerabilities from these files.
    - Internal/External Links: Maps out the website's structure and identifies connections to other domains.
    - Images, robots.txt, sitemap.xml: Gathers information about allowed/disallowed crawling paths and website structure.
    - Links in JavaScript, Wayback Machine: Uncovers hidden links and historical website data.
- `DNS Enumeration`: Queries over 40 DNS record types, including DMARC records for email security assessment.
- `Subdomain Enumeration`: Leverages multiple data sources (crt.sh, AnubisDB, ThreatMiner, CertSpotter, Facebook API, VirusTotal API, Shodan API, BeVigil API) to discover subdomains.
- `Directory Enumeration`: Supports custom wordlists and file extensions to uncover hidden directories and files.
- `Wayback Machine`: Retrieves URLs from the last five years to analyse website changes and potential vulnerabilities.

Installation:
```shell-session
AnasSalhen@htb[/htb]$ git clone https://github.com/thewhiteh4t/FinalRecon.git
AnasSalhen@htb[/htb]$ cd FinalRecon
AnasSalhen@htb[/htb]$ pip3 install -r requirements.txt
AnasSalhen@htb[/htb]$ chmod +x ./finalrecon.py
```
Important options:

| Option         | Description                                         |
| -------------- | --------------------------------------------------- |
| `-h`, `--help` | Show the help message and exit.                     |
| `--url`        | Specify the target URL.                             |
| `--headers`    | Retrieve header information for the target URL.     |
| `--sslinfo`    | Get SSL certificate information for the target URL. |
| `--whois`      | Perform a Whois lookup for the target domain.       |
| `--crawl`      | Crawl the target website.                           |
| `--dns`        | Perform DNS enumeration on the target domain.       |
| `--sub`        | Enumerate subdomains for the target domain.         |
| `--dir`        | Search for directories on the target website.       |
| `--wayback`    | Retrieve Wayback URLs for the target.               |
| `--ps`         | Perform a fast port scan on the target.             |
| `--full`       | Perform a full reconnaissance scan on the target.   |
