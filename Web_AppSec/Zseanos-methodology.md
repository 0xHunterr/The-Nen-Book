## XSS
- I test every parameter I find that is reflected not only for reflective XSS but for blind XSS as well.
- Check out https://github.com/0xInfection/Awesome-WAF for awesome research
- XSS is the easiest bug type to prevent against, so why are they creating a filter? And what else have they created filters around (think SSRF.. filtering just internal IP addresses? Perhaps they forgot about http://169.254.169.254/latest/meta-data - chances are, they did!).
##### Testing for XSS flow:
- How are “non-malicious” HTML tags such as `<h2>` handled?
- What about incomplete tags? `<iframe src=//zseano.com/c=`
- How do they handle encodings such as `<%00h2`? (There are LOTS to try here, `%0d, %0a, %09 etc)`
- Is it just a blacklist of hardcoded strings? Does `</script/x>` work? `<ScRipt>` etc.
- Checkout https://d3adend.org/xss/ghettoBypass for different encodings
- And [Browser's XSS Filter Bypass Cheat Sheet · masatokinugawa/filterbypass Wiki · GitHub](https://github.com/masatokinugawa/filterbypass/wiki/Browser's-XSS-Filter-Bypass-Cheat-Sheet)
## Cross Site Request Forgery (CSRF)


