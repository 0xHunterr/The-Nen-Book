# Passive Recon
- find public API documentations by searching the internet for `company_name API` or `company_name developer docs`
- `company_name inurl:swagger`, This documentation often includes all API endpoints even the private, their input parameters, and sample responses
- Google Dorking 
	- `inurl:"/wp-json/wp/v2/users"`
	- `intitle:"index.of" intext:"api.txt" `
	- `inurl:"/api/v1" intext:"index of /"`
- Shodan
	- `hostname:"targetname.com"`
	- `content-type: application/json`
	- `content-type: application/xml`
	- `wp-json`
- The Wayback Machine
	- Finding and comparing historical snapshots of API documentation can simplify testing for Improper Assets Management.
	- Test for older Versions ex-> `/api/v2/user_emails` -> `/api/v1/user_emails
# Active Recon
- search for other API endpoints by using recon techniques from [Web_Hacking_Reconnaissance](../Web_AppSec/Web_Hacking_Reconnaissance.md) 
- Try Fuzzing techniques for discovering API endpoints and resources, the best for APIs is Kite Runner
	- kiterunner:
```
kr scan HTTP://127.0.0.1 -w ~/api/wordlists/data/kiterunner/routes-large.kite
kr scan https://domain.com/api/ -w routes-large.kite -x 20
kr scan https://domain.com/api/ -A=apiroutes-220828 -x 20
kr brute https://domain.com/api/ -A=raft-large-words -x 20 -d=0
kr brute https://domain.com/api/ -w /tmp/lang-english.txt -x 20 -d=0
```
- nmap
	- `nmap -sC -sV [target]`
	- `nmap -p- [target]` all ports
	- `nmap -sV --script=http-enum [target] -p 80,443,8000,8080`
- amass: `amass enum -active/passive -d [target] | grep api`
- Map the the App while intercepting requests
- try to generate error messages in hopes that the API leaks information about itself
- understand each API endpoint’s functionality, parameters, and query structure
- Identify all the possible user data input locations
- Look out for any authentication mechanisms, including these:
	- Do access tokens expire when updating or resetting passwords?
	- What access tokens are needed?
	- Which endpoints require tokens and which do not?
	- How are access tokens generated?
	- Can users use the API to generate a valid token without logging in?
	