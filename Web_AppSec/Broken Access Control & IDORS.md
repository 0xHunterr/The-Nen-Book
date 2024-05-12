
IDORs happen when users can access resources that do not belong to them by directly referencing the object ID, object number, or filename.
==Remember that you can trigger IDORs from different locations within a request, like URL parameters, form fields, filepaths, headers, and cookies==

# Testing for IDOR - ( Manual-Method ):
## Basic Steps:

```md
1. Create two accounts if possible or else enumerate users first. 
2. Check if the endpoint is private or public and does it contains any kind of id param.
3. Try changing the param value to some other user and see if does anything to their account.
4. Done !!
```

# Prevention

1. The App should check the user’s identity and permissions before granting access to a resource. ( a MUST )
2. The website can use a unique, unpredictable key or a hashed identifier to reference each user’s resources.
# Methodology and Testing Checklist

You can find a pretty good one here [HowToHunt-IDOR](https://github.com/KathanP19/HowToHunt/blob/master/IDOR/IDOR.md)
- [ ] Test without Signing in See if you can use an unauthenticated session to access the information
- [ ] Create two accounts for each permission level.
- [ ] Use the highest-privileged account you own and go through the application
- [ ] Look for functionalities that return user information or modify user data and Note them
- [ ] Inspect each request and find the parameters that contain numbers, usernames, or IDs.
- [ ] Look for IDORs from different locations like URL parameters, form fields, filepaths, headers, and cookies
- [ ] Does the app ask for non-numeric IDs? Use numeric IDs instead ex (`album_id=MyPictures → album_id=12`)
- [ ] If a regular ID replacement isn’t working, try wrapping the ID in an array ex (`{“id”:19} → {“id”:[19]}`)
- [ ] Try replacing an ID with a wildcard (`GET /api/users/<user_id>/ → GET /api/users/*`)
- [ ] Look for APIs calls and Endpoints 
	- [ ] Check if the API is returning sensitive data
	- [ ] Check if the Data is filtered only by Client side ( see response in Burp)
	- [ ] Play with API versioning: `api/v1/users?userid=2` ->  `api/v2/users?userid=2`
- [ ] Found Encoded IDs?
	- [ ] Try to decode with different algos (base64url, base64, URL)
	- [ ] Use the Smart Decode tool in Burp’s decoder
- [ ] Hashed IDs?
	- [ ] See if the ID is predictable
	- [ ] Try creating a few accounts to analyze how these IDs are created. 
- [ ] Offer the Application an ID, Even If It Doesn’t Ask for One 
	- [ ] Append id, `user_id`, `message_id`, or other object references to the URL query, or the POST body parameters => GET` /api_v1/messages` => GET `/api_v1/messages?user_id=ANOTHER_USERS_ID`
	- [ ] Pro tip: You can find parameter names to try by deleting or editing other objects and seeing the parameter names used.
- [ ] Test for Blind IDOR
- [ ] Change the Request Method: 
      GET `example.com/uploads/user1236-01.jpeg`-> DELETE `example.com/uploads/user1236-01.jpeg`
- [ ] Change the Requested File Type:
       GET `/get_receipt?receipt_id=2983` -> GET `/get_receipt?receipt_id=2983.json`
- [ ] Got 401/403/404?
	- [ ] Try to fuzz there for different IDs and Try Directory Busting
# Bypassing IDOR Protection

IDORs aren’t always as simple as switching out a numeric ID, the way they reference resources also often becomes more complex

### Encoded IDs and Hashed IDs

- Don’t ignore encoded and hashed IDs likely to be (base64url, base64, URL)
- Use the Smart Decode tool in Burp’s decoder

If the application is using a hashed or randomized ID
- See if the ID is predictable
- try creating a few accounts to analyze how these IDs are created. 
- You might be able to find a pattern that will allow you to predict IDs belonging to other users.

### Offer the Application an ID, Even If It Doesn’t Ask for One

- If no IDs exist in the generated request, try adding one to the request
- Append id, `user_id`, `message_id`, or other object references to the URL query, or the POST body parameters GET` /api_v1/messages` => GET `/api_v1/messages?user_id=ANOTHER_USERS_ID`

### Keep an Eye Out for Blind IDORs

sometimes endpoints susceptible to IDOR don’t respond with the leaked information directly.
For example, imagine that this endpoint on example.com allows users to email themselves a copy of a receipt:

```
POST /get_receipt
(POST request body)
receipt_id=3001
```
this will send the receipt with id= 3001 info to the Email of the current user,  if we tried a receipt belongs to another user like 2983
### Change the Request Method

Applications often enable multiple request methods on the same endpoint but fail to implement the same access control for each method:
GET `example.com/uploads/user1236-01.jpeg`-> DELETE `example.com/uploads/user1236-01.jpeg`
### Change the Requested File Type

Applications might be flexible about how the user can identify information
GET `/get_receipt?receipt_id=2983` -> GET `/get_receipt?receipt_id=2983.json`

# Escalating the Attack

- The impact of an IDOR depends on the affected function
- Read-based IDORs -> look for sensitive information in the application (direct messages, personal information, and private content)
- Write-based IDORs-> (password reset, password change, and account recovery features, email subscription settings)
- Write-based IDOR can be combined with self-XSS to form a stored XSS.
- An IDOR on a password reset endpoint combined with username enumeration can lead to a mass account takeover. 
- Write IDOR on an admin account may even lead to RCE!
# Automating the Attack

- A very Good reference you can find here-> https://www.tevora.com/threat-blog/finding-broken-access-controls/
- Burp intruder to iterate through IDs to find valid ones, The Burp extension [Autorize](https://github.com/Quitten/Autorize/) scans for authorization issues by accessing higher-privileged accounts with lower-privileged accounts, whereas the Burp extensions [Auto Repeater](https://github.com/nccgroup/AutoRepeater/) and [AuthMatrix](https://github.com/SecurityInnovation/AuthMatrix/) allow you to automate the process of switching out cookies, headers, and parameters.

# Finding Your First IDOR!

1. Create two accounts for each application role and designate one as the attacker account and the other as the victim account
2. Pay attention to features that return sensitive information or modify user-data
3. Revisit the features you discovered in step 2. With a proxy, intercept your browser traffic while you browse through the sensitive functionalities.
4. With a proxy, intercept each sensitive request and switch out the IDs that you see in the requests. If switching out IDs grants you access to other users’ information or lets you change their data, you might have found an IDOR.
5. try a protection-bypass technique
6. Monitor for information leaks in export files, email, and text alerts. An IDOR now might lead to an info leak in the future.
7. Draft your first IDOR report
# Reference

- [WSTG - v4.2](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References.html)
- [Everything You Need to Know About IDOR (Insecure Direct Object References)](https://medium.com/@aysebilgegunduz/everything-you-need-to-know-about-idor-insecure-direct-object-references-375f83e03a87)
- [Finding more IDORs - Tips and Tricks | Aon](https://www.aon.com/cyber-solutions/aon_cyber_labs/finding-more-idors-tips-and-tricks/)
- [OWASP Cheat Sheet-Preventing IDORS](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)
- [Automating Finding IDORs](https://www.yeswehack.com/learn-bug-bounty/pimpmyburp-pwnfox-autorize-find-idor)
