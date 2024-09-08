## How to Detect
-  fuzzing the template by injecting a sequence of special characters such as `${{<%[%'"}}%\` ->If an exception is raised, this indicates that the injected template syntax is potentially being interpreted by the server in some way
##### Plaintext context
- It's about rendering your input server side into HTML or sort of form before generating the response like `render('Hello ' + username)` 
- It's like a simple XSS here, However, by setting mathematical operations as the value of the parameter, we can test whether this is also a potential entry point for a server-side template injection attack `http://vulnerable-website.com/?username=${7*7}`
##### Code context
-  the vulnerability is exposed by user input being placed within a template expression
- first establish that the parameter doesn't contain a direct XSS vulnerability by injecting arbitrary HTML into the value: `http://vulnerable-website.com/?greeting=data.username<tag>`
  In the absence of XSS, this will usually either result in a blank entry in the output (just `Hello` with no username),
- try and break out of the statement using common templating syntax and attempt to inject arbitrary HTML after it: `http://vulnerable-website.com/?greeting=data.username}}<tag>` Try different syntax
## Identify
- submitting invalid syntax ex: `<%=foobar%>``<%=foobar%>`
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240908181755.png)
-  same payload can sometimes return a successful response in more than one template language. For example, the payload `{{7*'7'}}` returns `49` in Twig and `7777777` in Jinja2
-  