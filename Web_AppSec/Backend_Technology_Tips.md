
# .NET
## Information Gathering
- Cookie (ASP.NET_SessionId, ASPXAUTH) 
- Server Headers 
- ViewState 
- Response Header (X-Powerded-By, X-AspNet-Version) 
- Error Messages
## Vulnerabilities
- Low Hanging Fruits
- Server Information Disclosure
- Lack of Security Headers
- IIS Default Page Disclosure
- Improper Error Handling
- ASP.NET Debugging Enabled
- Directory Listing
- ASP.NET ViewState Vulnerabilities

### Improper Error Handling - .NET
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240829162551.png)

### ASP.NET Debugging Enabled - .NET
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240829162711.png)

### Server Information Disclosure - .NET
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240829162337.png)

### IIS Default Page Disclosure - .NET
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240829162451.png)


### ASP.NET ViewState Vulnerabilities
- MAC Disabled
- MAC Enabled (encryption key via brute-force)
- Web config file

# Node.js
## Information Gathering
- Cookie (connect.sid)
- Server Headers
- Response Header (X-Powerded-By)
## Vulnerabilities
### SQL Injection - Node.js:
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240829161235.png)

### XSS - Node.js
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240829161437.png)

### Improper Authentication and Authorization – Node.js
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240829161625.png)

### IDOR - Node.js
![](../Media/Web%20AppSec%20Images/Pasted%20image%2020240829161959.png)

# Java
## Information Gathering
- Cookie (JSESSIONID) 
- Server Headers (Tomcat, WebLogic, JBoss) 
- Endpoints (JSP) 
- Response Header (X-Powerded-By:Servlet) 
- Error Messages
