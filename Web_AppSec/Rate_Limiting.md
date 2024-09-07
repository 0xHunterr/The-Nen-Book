- some websites may exclude these types of vulnerabilities from the scope, So read the scope well before testing! 
- You can find it in any submission form like (login forms, contact forms, registration forms, and other types of forms that accept user input).
## Testing and Checklist
-  look for any submission form like (login forms, contact forms, registration forms, and other types of forms that accept user input).
- Try to submit a lot of submissions using Intruder, FFUF, ZAP...etc.
- If u encountered a rate limit technique try to identify how is working and how it identifies u.
```http
# IP based bypass
X-Forwarded-For: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Host: 127.0.0.1
X-Forwared-Host: 127.0.0.1

# Double X-Forwarded-For header example
X-Forwarded-For:
X-Forwarded-For: 127.0.0.1
```
- Altering other request headers such as the user-agent and cookies is recommended
- If the target system applies rate limits on a per-account or per-session basis, distributing the attack or test across multiple accounts or sessions can help in avoiding detection
- Inserting blank bytes like `%00`, `%0d%0a`, `%0d`, `%0a`, `%09`, `%0C`, `%20` into code or parameters can be a useful strategy. like-> `code=1234%0a`

## Impact
-  risk of the email address domain being added to a spam list.
-  reputational damage for the business as customers’ trust is impacted through receiving large amounts of unwanted and unsolicited emails.
- for systems that use Software-as-a-Service (SaaS) email providers, there can be direct financial costs associated with sending large volumes of emails to unconfirmed user’s emails.