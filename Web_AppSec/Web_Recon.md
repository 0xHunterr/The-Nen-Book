# First Manually Walking Through the Target

> Try to uncover every feature in the application that users can access by browsing through every page and clicking every link. Access the functionalities that you don’t usually use.

# Google Dorking

Google can be a means of discovering valuable information such as hidden admin portals, unlocked password files, and leaked authentication keys like (`inurl`, `site`, `intitle`, `link`, `filetype`, `Wildcard (*)` , `Quotes (" ")`, `Or (|)`, `Minus (-)` ).

1. look for all of a company’s subdomains by: `site:*.example.com`
2. A compromised Kibana instance can allow attackers to collect extensive information about a site’s operation. Many Kibana dashboards run under the path `app/kibana`, so this query will reveal whether the target has a Kibana dashboard. You can then try to access the dashboard to see if it’s unprotected: site:example.com `inurl:app/kibana`
3. Google can find company resources hosted by a third party online, such as Amazon S3 buckets : `site:s3.amazonaws.com COMPANY_NAME`
4. Look for special extensions that could indicate a sensitive file: `site:example.com ext: (php | log | jsp | asp | log)`
5. use **google hacking database** It contains many search queries that could be helpful to you during the recon process.

# WHOIS and Reverse WHOIS

You might be able to find the associated contact information, such as an email, name, address, or phone number: `$ whois facebook.com`

1. Find the IP address of a domain you know by running the nslookup command:`$ nslookup facebook.com`
2.  Reverse IP lookup and IP ranges (CIDRs) and also run `whois` on the IP, Reverse IP searches look for domains hosted on the same server:
```
$ whois 157.240.2.35
NetRange: 157.240.0.0 - 157.240.255.255
CIDR: 157.240.0.0/16
NetName: THEFA-3
NetHandle: NET-157-240-0-0-1
Parent: NET157 (NET-157-0-0-0-0)
NetType: Direct Assignment
OriginAS:
Organization: Facebook, Inc. (THEFA-3)
RegDate: 2015-05-14
Updated: 2015-05-14
Ref: https://rdap.arin.net/registry/ip/157.240.0.0
OrgName: Facebook, Inc.
```
3. Autonomous system numbers (ASNs) identify the owners of these networks. By checking if two IP addresses share an ASN:
   - run several IP-to ASN translations to see if the IP addresses map to a single ASN. If many addresses within a range belong to the same ASN, the organization might have a dedicated IP range
```
# -h flag in the whois command sets the WHOIS server to retrieve information from,
# and whois.cymru.com is a database that translates IPs to ASNs.

$ whois -h whois.cymru.com 157.240.2.20
AS | IP | AS Name
32934 | 157.240.2.20 | FACEBOOK, US

$ whois -h whois.cymru.com 157.240.2.27
AS | IP | AS Name
32934 | 157.240.2.27 | FACEBOOK, US
```
# Subdomain Enumeration
After finding as many domains on the target as possible, locate as many subdomains on those domains as you can. 
Each subdomain represents a new angle for attacking the network, good tool for that is [subfinder](https://github.com/projectdiscovery/subfinder)You can find some good wordlists made by other hackers online [SecLists](https://github.com/danielmiessler/SecLists/) 
use a wordlist generation tool like [Commonspeak2](https://github.com/assetnote/commonspeak2/) to generate wordlists based on the most current internet
data. 
You can find subdomains of subdomains by running enumeration tools recursively: add the results of your first run to your Known Domains list and run the tool again.

```bash
gobuster dns -d target_domain -w wordlist
```

# Service Enumeration

it can be done in two ways first is **active** scan using `nmap` or `masscan` or **Passive** using **shodan , Censys, Project Sonar** it gives a lot of valuable info so it’s so important

# Directory Brute-Forcing

The next thing you can do to discover more of the site’s attack surface is brute-force the directories of the web servers you’ve found. you might discover (hidden admin panels, configuration files, password files, outdated function alities, database copies, and source code files). 
Directory brute-forcing can sometimes allow you to directly take over a server! You can use `Dirsearch` or `Gobuster` for directory brute-forcing. `Gobuster`’s Dir mode is used to find additional content on a specific domain or subdomain. This includes hidden directories and files.

```bash
gobuster dir -u target_url -w wordlist
./dirsearch.py -u [scanme.nmap.org](http://scanme.nmap.org/) -e php
```

use a screenshot tool like [EyeWitness ](https://github.com/FortyNorthSecurity/EyeWitness/)or [Snapper](Web%20AppSec/Web%20Vulnerabilities/\[https:/github.com/dxa4481/Snapper/]\(https:/github.com/dxa4481/\)/) to automatically verify that a page is hosted on each location. EyeWitness accepts a list of URLs and takes screenshots of each page.

# Spidering the Site

Another way of discovering directories and paths is through Web Spidering, or web crawling, a process used to identify all pages on a site. A web spider tool starts with a page to visit. It then identifies all the URLs embedded on the page and visits them. By recursively visiting all URLs found on all pages of a site, the web spider can uncover many hidden endpoints in an application. 
[OWASP Zed Attack Proxy **(ZAP)**](https://www.zaproxy.org/) has a built-in web spider you can use Access its spider tool by opening ZAP and choosing Tools Spider. OR using Command Line Tool like [**Katana**](https://github.com/projectdiscovery/katana) from Project Discovery
# Third-Party Hosting

* Organizations can pay to store resources in buckets to serve in their web applications, or they can use S3 buckets as a backup or storage location Most buckets use the URL format `BUCKET.s3.amazonaws.com` or `http://s3.amazonaws.com/BUCKET`, so the following search terms are likely to find results:( `site:s3.amazonaws.com COMPANY_NAME` , `site:amazonaws.com COMPANY_NAME`)
* Companies often still place keywords like `aws` and `s3` in their custom bucket URLs, so try these searches: (`amazonaws s3 COMPANY_NAME`, `amazonaws bucket COMPANY_NAME`, `amazonaws COMPANY_NAME`, `s3 COMPANY_NAME`)
* [GrayhatWarfare](https://buckets.grayhatwarfare.com/) is an online search engine you can use to find publicly exposed S3 buckets
* Another good tool is [Bucket Stream](https://github.com/eth0izzle/), which parses certificates belonging to an organization and finds S3 buckets based on permutations of the domain names found on the certificates. it also automatically checks whether the bucket is accessible, so it saves you time use the AWS command line tool to see if u can access one. Install the tool by using the following command: `pip install awscli`
* configure it to work→ [https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)

# GitHub Recon

> Start by finding the GitHub usernames relevant to your target by searching product names via GitHub’s search bar, When you’ve found usernames visit their pages. Find repositories related to the projects you’re testing and record them, along with the usernames of the organization’s top contributors
>
> For each repository, pay special attention to the Issues and Commits sections. These sections are full of potential info leaks: unresolved bugs, Recent code changes that haven’t stood the test of time are more likely to contain bugs

1. Look at any protection mechanisms implemented to see if you can bypass them
2. search the Code section for potentially vulnerable code snippets.
3. Once you’ve found a file of interest, check the Blame and History sections at the top-right corner of the file’s page to see how it was developed
4. look for hardcoded secrets such as API keys, encryption keys, and database passwords. Search the organization’s repositories for terms like: (key, secret, and password) to locate hardcoded user credentials that you can use to access internal systems.
5. After you’ve found leaked credentials, you can use [KeyHacks](https://github.com/streaak/keyhacks/) to check if the credentials are valid and learn how to use them to access the target’s services.
6. See if any of the source code deals with important functions such as authentication, password reset, state-changing actions, or private info reads. Pay attention to code that deals with user input, such as HTTP request parameters, HTTP headers, HTTP request paths, database entries, file reads, and file uploads
7. Look for any configuration files, Also search for old endpoints and S3 bucket URLs that you can attack. Record these files for further review in the future.
8. Pay attention to dependencies and imports being used and go through the versions list to see if they’re outdated and record them. You can use this information later to look for publicly disclosed vulnerabilities that would work on your target.
9. Tools like [Gitrob](https://github.com/michenriksen/gitrob/) locates potentially sensitive files pushed to public repositories and [TruffleHog](https://github.com/trufflesecurity/truffleHog/) specializes in finding secrets in repositories by conducting regex searches and scanning for high-entropy strings, These tools can automate the GitHub recon process.

# Creating Your own Scripts

Take a look at [[Bash_Scripting]]
# Summary
Here you can find an extensive summary of all the tools mentioned in this article and the whole process
## Scope Discovery

* WHOIS looks for the owner of a domain or IP.
* ViewDNS.info [Reverse WHOIS](https://viewdns.info/reversewhois/) is a tool that searches for reverse WHOIS data by using a keyword.
* nslookup queries internet name servers for IP information about a host.
* [ViewDNS](https://viewdns.info/reverseip/) reverse IP looks for domains hosted on the same server, given an IP or domain.
* [crt.sh](https://crt.sh/), [Censys](https://censys.io/), and [Cert Spotter](https://sslmate.com/certspotter/) are platforms you can use to find certificate information about a domain.
* [Subfinder](./), [Sublist3r](https://github.com/aboul3la/Sublist3r/), [SubBrute](https://github.com/TheRook/subbrute/), [Amass](https://github.com/OWASP/Amass/), and [Gobuster](https://github.com/OJ/gobuster/) enumerate subdomains.
* Daniel Miessler’s [SecLists](https://github.com/danielmiessler/SecLists/) is a list of keywords that can be used during recon and hacking. ex (brute-force subdomains and filepaths).
* [Commonspeak2](https://github.com/assetnote/commonspeak2/) generates lists that can be used to brute-force subdomains and filepaths using publicly available data.
* [Altdns](https://github.com/infosec-au/altdns) brute-forces subdomains by using permutations of common subdomain names.
* [Nmap](https://nmap.org/) and [Masscan](https://github.com/robertdavidgraham/masscan/) scan the target for open ports.
* [Shodan](https://www.shodan.io/), [Censys](https://censys.io/), and [Project Sonar](https://www.rapid7.com/research/project-sonar/) can be used to find services on targets without actively scanning them.
* [Dirsearch](https://github.com/maurosoria/dirsearch/) and [Gobuster](https://github.com/OJ/gobuster) are directory brute-forcers used to find hidden filepaths. [EyeWitness](https://github.com/FortyNorthSecurity/EyeWitness/) and [Snapper](https://github.com/dxa4481/Snapper/) grab screenshots of a list of URLs, They can be used to quickly scan for interesting pages among a list of enumerated paths
* [OWASP ZAP](https://owasp.org/www-project-zap/) is a security tool that includes a scanner, proxy, and much more. Its web spider can be used to discover content on a web server.
* [GrayhatWarfare](https://buckets.grayhatwarfare.com/) is an online search engine you can use to find public Amazon S3 buckets.
* [Lazys3](https://github.com/nahamsec/lazys3/) and [Bucket Stream](https://github.com/eth0izzle/bucket-stream/) brute-force buckets by using keywords.

## OSINT

* The [Google Hacking Database](https://www.exploit-db.com/google-hacking-database/) contains useful Google search terms that frequently reveal vulnerabilities or sensitive files.
* [KeyHacks](https://github.com/streaak/keyhacks/) helps you determine whether a set of credentials is valid and learn how to use them to access the target’s services.
* [Gitrob](https://github.com/michenriksen/gitrob/) finds potentially sensitive files that are pushed to public repositories on GitHub.
* [TruffleHog](https://github.com/trufflesecurity/truffleHog/) specializes in finding secrets in public GitHub repositories by searching for string patterns and high-entropy strings.
* [PasteHunter](https://github.com/kevthehermit/PasteHunter/) scans online paste sites for sensitive information.
* [Wayback Machine](https://archive.org/web/) is a digital archive of internet content, You can use it to find old versions of sites and their files.
* [Waybackurls](https://github.com/tomnomnom/waybackurls/) fetches URLs from the Wayback Machine

## Tech Stack Fingerprinting

* [Wappalyzer](https://www.wappalyzer.com/) identifies content management systems, frameworks, and programming languages used on a site.
* The [CVE database](https://cve.mitre.org/cve/search\_cve\_list.html) contains publicly disclosed vulnerabilities, You can use its website to search for vulnerabilities that might affect your target.
* [BuiltWith](https://builtwith.com/) is a website that shows you which web technologies a website is built with
* [StackShare](https://stackshare.io/) is an online platform that allows developers to share the tech they use, You can use it to collect information about your target.
* [Retire.js](https://retirejs.github.io/retire.js/) detects outdated JavaScript libraries and Node.js packages

## Automation

Once you have a solid understanding of how to conduct recon on a target, you can try to leverage recon platforms like [Nuclei](https://github.com/projectdiscovery/ nuclei/) or [Intrigue Core](https://github.com/intrigueio/intrigue-core/) to make your recon process more efficient. But when you’re starting out, I recommend that you do recon manually with individual tools or write your own automated recon scripts to learn about the process for Bash Script tutorial visit [Bash_Scripting](Bash_Scripting.md)
