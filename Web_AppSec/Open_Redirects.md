# What is Open Redirect ?
Sites often use HTTP or URL parameters to redirect users to a specified URL without any user action. While this behavior can be useful, it can also cause open redirects, which happen when an attacker is able to manipulate the value of this parameter to redirect the user offsite. Let’s discuss this common bug, why it’s a problem

# Prevention
- implement URL validators with either a blocklist or an allowlist.
- Check the Referrer When Doing Redirects
- Disallow Offsite Redirects(prevent redirects to other domains by checking the URL being passed to the redirect function. Make sure all redirect URLs are **relative paths**(they start with a single `/` character))
-  Check Client-Side Code Too! (Redirects can happen in client-side JavaScript, too! Validate any code that sets `window.location`, to ensure the URL is not taken from untrusted input, take a look at [Web Security Academy](https://portswigger.net/web-security/dom-based/open-redirection)
# code samples
The code samples below demonstrate how to check that a URL is a relative path.

Python 
````python
import re

def is_relative(url):
  return re.match(r"^\/[^\/\\]", url)
````

Node
```javascript
function isRelative(url) {
  return url && url.match(/^\/[^\/\\]/);
}
```
# Hunting for Open Redirects
### Step 1: Look for Redirect Parameters
These often show up as URL parameters like:
`https://example.com/login?redirect=https://example.com/dashboard
`https://example.com/login?redir=https://example.com/dashboard
`https://example.com/login?next=https://example.com/dashboard
`https://example.com/login?next=/dashboard`
Note that not all redirect parameters have straightforward names like redirect or redir
==You should record all parameters that seem to be used for redirect, regardless of their parameter names==

take note of the pages that don’t contain redirect parameters in their URLs but still automatically redirect their users. for referer-based open redirects.
To find these pages, you can keep an eye out for 3XX response codes like 301 and 302.
### Step 2: Use Google Dorks to Find Additional Redirect Parameters
look for pages that contain URLs in their URL parameters.
you can search for terms like **=http** and **=https**, which are indicators of URLs in a parameter. 
The following searches for URL parameters that contain absolute URLs:
`inurl:%3Dhttp site:example.com`

for relative URLs we can add the `%2F` the URL-encoded version of the slash (/), the following search term searches URLs that contain =/, and therefore returns URL parameters that contain relative URLs: `inurl:%3D%2F site:example.com` 

Alternatively, you can search for the names of common URL redirect parameters.
```
inurl:redir site:example.com
inurl:redirect site:example.com
inurl:redirecturi site:example.com
inurl:redirect_uri site:example.com
inurl:redirecturl site:example.com
inurl:redirect_uri site:example.com
inurl:return site:example.com
inurl:returnurl site:example.com
inurl:relaystate site:example.com
inurl:forward site:example.com
inurl:forwardurl site:example.com
inurl:forward_url site:example.com
inurl:url site:example.com
inurl:uri site:example.com
inurl:dest site:example.com
inurl:destination site:example.com
inurl:next site:example.com
```

### Step 3: Test for Parameter-Based Open Redirects
Test each Param for an open redirect. Insert a random hostname, or a hostname you own, into the redirect parameters; then see if the site automatically redirects to the site you specified like:
`https://example.com/login?n=http://google.com` 
Some sites will redirect to the destination site immediately after you visit the URL, without any user interaction. But for a lot of pages, the redirect won’t happen until after a user action, like registration, login, or logout so be sure to carry out the required user interactions before checking for the redirect
### Step 4: Test for Referer-Based Open Redirects
To test for these, set up a page on a domain you own and host this HTML page:
```
<html>
 <a href="https://example.com/login">Click on this link!</a>
</html>
```
Replace the linked URL with the target page. Then reload and visit your HTML page. Click the link and see if you get redirected to your site automatically or after the required user interactions

# Bypassing Open-Redirect Protection
open redirects in almost all the web targets, Sites prevent open redirects by validating the URL used to redirect the user, making the root cause of open redirects failed URL validation. And, unfortunately, URL validation is extremely difficult to get right.

Here, you can see the components of a URL. The way the browser redirects the user depends on how the browser differentiates between these components:
```
scheme://userinfo@hostname:port/path?query#fragment
```
Browsers redirect users to the location indicated by the `hostname` section of the URL. However, URLs don’t always follow the strict format shown in this example. let's discuss some strategies:
### 1- Using Browser Autocorrect, Blocklist Bypass
Modern browsers often autocorrect URLs that don’t have the correct components
For example, Chrome will interpret all of these URLs as pointing to `https://attacker.com`:
```
https:attacker.com
https;attacker.com
https:\/\/attacker.com
https:/\/\attacker.com
```
These quirks can help you bypass URL validation based on a blocklist. For example, if the validator rejects any redirect URL that contains the strings https:// or http://, you can use an alternative string, like `https;` and the same thing for  u can replace forward slash with backslash `http;\\`

### 2- Exploiting Flawed Validator Logic, Allowlist Bypass
the URL validator often checks if the redirect URL starts with, contains, or ends with the site’s domain name.
You can bypass this type of protection by creating a subdomain or directory with the target’s domain name:
```
https://example.com/login?redir=http://example.com.attacker.com 
https://example.com/login?redir=http://attacker.com/example.com
```
To prevent these attacks, the validator might accept only URLs that both start and end with a domain listed on the allowlist.
However, it’s possible to construct a URL that satisfies both of these rules. Take a look at :
`https://example.com/login redir=https://example.com.attacker.com/example.com`
This URL redirects to attacker.com, The browser will interpret the first `example.com` as the subdomain name and the second one as the File Path.

Or you could use the at symbol (@) to make the first `example.com` the username portion of the URL `https://example.com/login?redir=https://example.com@attacker.com/example.com`
Custom-built URL validators are prone to attacks like these, because developers often don’t consider all edge cases
### 3- Using Data URLs
data URLs use the data: scheme to embed small files in a URL. They are constructed in this format: `data:MEDIA_TYPE[;base64],DATA`      examples-> `data:text/plain,hello!`   

we can use the encoding option to embed a piece of code that redirect the user and bypass the protection as `<script>location="https://example.com"</script>` would be like this:
`https://example.com/login?redir=data:text/html;base64, PHNjcmlwdD5sb2NhdGlvbj0iaHR0cHM6Ly9leGFtcGxlLmNvbSI8L3NjcmlwdD4=`
### 4- Exploiting URL Decoding
the URLs sent over the internet have to be `URL encoded`, so when a browser/Validator deals with these URLs they have to decode them first and find out what's contained in them so

If there is any inconsistency between how the validator and browsers decode URLs, you could exploit that to your advantage:
1. Try to double and triple encode a certain chars like the (/) in the file path
   `https:// example.com/@attacker.com -> https://example.com%252f@attacker.com`
2. Try the non-Ascii chars like `https://attacker.com╱.example.com` here validator consider the hostname is `example.com` but the browser determine it's `attacker.com` and ==there's many scenarios== 
3. find look-alike characters and insert them in URLs to bypass filters. The Cyrillic character set is especially useful since it contains many characters similar to ASCII characters.

>    To defeat more-sophisticated URL validators, combine multiple strategies to bypass layered defenses. I’ve found the following payload to be useful: `https://example.com%252f@attacker.com/example.com`

take a look at [Book of Bugbounty Tips](https://gowsundar.gitbook.io/book-of-bugbounty-tips/open-redirect) for more Tips!.

# Escalating the Attack
1. in phishing attaacks `https://example.com/login?next=https://attacker.com/fake_login.html` it would redirect them to the attacker’s site after login and ask them to provide the creds again because they were wrong 
2. an open redirect can help you bypass URL blocklists and allowlist. 
3. `https://example.com/?next=https://attacker.com/` This URL will pass even well-implemented URL validators an attacker can utilize an open redirect within those allowlisted pages to redirect the request anywhere. You could also use open redirects to steal credentials and OAuth tokens


# Finding Your First Open Redirect!

1. Search for redirect URL parameters. These might be vulnerable to parameter-based open redirect 
2. Search for pages that perform referer-based redirects. These are candidates for a referer-based open redirect.
3. Test the pages and parameters you’ve found for open redirects
4. If the server blocks the open redirect, try the protection bypass techniques mentioned in this chapter.
5. Brainstorm ways of using the open redirect in your other bug chains!
# References

- [Bug Bounty Bootcamp book](https://www.amazon.eg/-/en/Bug-Bounty-Bootcamp-Reporting-Vulnerabilities/dp/1718501544)
- https://pentester.land/blog/open-redirect-cheatsheet/
- https://pentestbook.six2dez.com/enumeration/web/open-redirects
- https://portswigger.net/support/using-burp-to-test-for-open-redirections
- https://portswigger.net/web-security/dom-based/open-redirection
- https://application.security/free-application-security-training/owasp-top-10-insecure-url-redirect
- https://book.hacktricks.xyz/pentesting-web/open-redirect
- https://www.hacksplaining.com/exercises/open-redirects
- https://gowsundar.gitbook.io/book-of-bugbounty-tips/open-redirect
- https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Open%20Redirect
- https://github.com/KathanP19/HowToHunt/tree/master/Open_Redirection
- [H1 Reports]( https://nored0x.github.io/penetration%20testing/writeups-Bug-Bounty-hackrone/#open-redirect)
- [H1 Reports Repo](https://github.com/reddelexc/hackerone-reports/blob/master/tops_by_bug_type/TOPOPENREDIRECT.md)