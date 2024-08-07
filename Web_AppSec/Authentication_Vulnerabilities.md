## Testing Methodology
### Weak Password Complexity Requirements
- Review the website for any description of the rules.
- If self registration is possible, attempt to register several accounts with different kinds of weak passwords to discover what rules are in place.
	- Very short or blank. 
	- Common dictionary words or names. 
	- Password is the same as the username.
- If you control a single account and password change is possible, attempt to change the password to various weak values
### Improper Restriction of Authentication Attempts
- Manually submit several bad login attempts for an account you control. 
- After 10 failed login attempts, if the application does not return a message about account lockout, attempt to log in correctly. If it works, then there is no lockout mechanism. 
	- Run a brute force attack to enumerate the valid password. Tools: Hydra, Burp Intruder, etc. 
- if the account is locked out, monitor the requests and responses to determine if the lockout mechanism is insecure. Improper Restriction of Authentication Attempts 
  NOTE: Apply this test on all authentication pages
### Verbose Error Message
- Perform a successful login while monitoring all traffic in both directions between the client and server
- Look for instances where credentials are submitted in a URL query string or as a cookie, or are transmitted back from the server to the client.
- Attempt to access the application over HTTP and if there are any redirections to HTTPS
### Insecure Forgot Password Functionality
- Identify if the application has any forgotten password functionality
- If it does, perform a complete walk-through of the forgot password functionality using an account you have control of while intercepting the requests / responses in a proxy
- Review the functionality to determine if it allows for username enumeration or bruteforce attacks
- If the application generates an email containing a recovery URL, obtain a number of these URLs and attempt to identify any predictable patterns or sensitive information included in the URL. Also check if the URL is long lived and does not expire
### Defects in Multistage Login Mechanism
- Identify if the application uses a multistage login mechanism.
- If it does, perform a complete walk-through using an account you have control of while intercepting the requests / responses in a proxy
- Review the functionality to determine if it allows for username enumeration or bruteforce attacks.
## Bypasses
-  Try `X-Forward-For: 127.0.0.1` and change the IP every time you got blocked
-  Try to log-in to your own account successfully In some implementations, the counter for the number of failed attempts resets if the IP owner logs in successfully

# Something to know
- User rate limiting is sometimes preferred to account locking due to being less prone to username enumeration and denial of service attacks, However, it is still not completely secure
- HTTP basic authentication `Authorization: Basic base64(username:password)`
	- often don't support brute-force protection. As the token consists exclusively of static values, this can leave it vulnerable to being brute-forced.
	- vulnerable to session-related exploits, notably [CSRF](https://portswigger.net/web-security/csrf), against which it offers no protection on its own.
# References
- [Rana Khalil Web Security Academy](https://www.youtube.com/watch?v=mox6DL4zK7I&list=PLuyTk2_mYISJmmOLYzGxVSwS5vAhBVphr&index=1)
- [PortsWigger](https://portswigger.net/web-security/authentication)
- [ WSTG - v4.2](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/04-Authentication_Testing/)
- [OWASP ASVS V2 Authentication Verification Requirements](https://owasp.org/www-pdf-archive/OWASP_Application_Security_Verification_Standard_4.0- en.pdf)