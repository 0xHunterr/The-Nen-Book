# Step 1: Nmap
Running an `nmap` scan to understand the following:
- What is running on the target, what **_server names_**, what **_versions_**?
	- Service version scan
	- OS detection
	- Default script scan
- What **_client software_** would we need to connect and assess the target?

```bash
# Nmap help message output
nmap -h
```

```bash
# relaiable and fast enough 
sudo nmap -Pn -p- -A -T4 -oN scan.txt <target_ip>
```

```bash
# Faster, target may drop packets, adjust the '--min-rate' if needed
sudo nmap -Pn -p- -A --min-rate 5000 -oN scan.txt <target_ip>
```

```bash
# UDP scan example using '-T4', as going too fast may miss ports
sudo nmap -Pn -sU --top-ports 500 -A -T4 -oN udp-scan.txt <target_ip>
```

## Nmap scripts: not the default ones 
 There are additional `enum` and `brute` scripts that often don't get run as a default scan.
 Take note of everything u got such as:
- FTP file enumeration
- NFS share names
- HTTP robots.txt and redirects
- DNS names
- Emails
- Computer names
- OS versions

# Step 2.0 Service Enum: Active Directory 

A typical port signature for an Active Directory domain controller, especially apparent due to DNS, SMB, Kerberos, and LDAP being open on the box:
```text
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
```
## Identify the Local Domain
we should check the `nmap` output for the [RootDSE](https://www.ibm.com/docs/en/zos/2.4.0?topic=considerations-root-dse&ref=benheater.com) and any potential hostname (e.g. `DC01.domain.tld`). Once, established, we should add the domain and hostname to our `/etc/hosts` file.

```bash
target_ip='10.10.10.22'
sudo nmap -Pn --script ldap-rootdse.nse $target_ip
```

If you've only run a basic `nmap` scan and need to enumerate the `RootDSE`

```bash
target_ip='10.10.10.22'
target_domain='domain.tld'
target_hostname="DC01.${target_domain}"

echo -e "${target_ip}\t\t${target_domain} ${target_hostname}" | sudo tee -a /etc/hosts`
```
## DNS
If we've established the local domain for the Active Directory environment, we should attempt to enumerate any DNS records for use when assessing other protocols

Attempt for a zone transfer from the DNS server on the target.
==Note that: If the service configured correctly, the zone transfer should be refused:==
```bash
host -T -l $target_domain $target_ip
```

If the zone transfer fails, you can try and manually enumerate records in the target domain
```bash
target_ip='10.10.10.22'
target_domain='domain.tld'
dns_wordlist='/usr/share/seclists/Discovery/DNS/namelist.txt'
gobuster dns -r $target_ip -d $target_domain -w $dns_wordlist -t 100
```

## LDAP
A quick win would be the ability to enumerate LDAP records anonymously, as this would allow us to gather great information about users, groups, and other domain records.
```bash
target_domain='domain.tld'
target_hostname="DC01.${target_domain}"
domain_component=$(echo $target_domain | tr '\.', '\n' | xargs -I % echo "DC=%" | paste -sd, -)
ldapsearch -x -H ldap://$target_hostname -b $domain_component
```
==Note that: If configured correctly, you should see an error saying that a successful bind must be completed, meaning you need a credential==
However, if you are able to anonymously query LDAP, this is an example command to pull everything from LDAP:
```bash
ldapsearch -x -H ldap://$target_hostname -b $domain_component 'objectClass=*'
```
## SMB
If we can connect to SMB anonymously 
- Checking the shares for any useful info 
- it's worth checking to see if we can enumerate object RIDs anonymously as well. RID cycling would allow us to enumerate a list of users and groups on the computer for further use during testing.
- Connect to SMB with a null session (and maybe even list shares), we can try and enumerate more and potentially map shares
```bash
smbclient -N -L //$target_ip
```
- Connect to a SMB share via null session
```bash
smbclient -N //$target_ip/share_name
```

```bash
# nxc replaces crackmapexec
nxc smb $target_ip -u 'anonymous' -p '' --rid-brute
nxc smb $target_ip -u '' -p '' --rid-brute 
```
==Note that: If configured correctly, you should see a permissions error, indicating the tests have failed==

## Kerberos
- If u haven't got any usernames from the previous approaches try the Pre-Auth username Enum
- If we've found some usernames, we can then see if any of them are configured with `UF_DONT_REQUIRE_PREAUTH`, pull some AS-REP hashes, and attempt to crack them offline.
### Pre-Auth Username Enumeration
- Kerberos is an authentication protocol used in networks.
- **Pre-Auth Username Enumeration** is a technique to find valid usernames by checking how the Kerberos server (KDC) responds to requests without pre-authentication.
#### How it Works:
1. Send a request for a Ticket Granting Ticket (TGT) to the KDC without providing a pre-authentication hash.
2. If the username is valid:
   - The KDC will either ask for pre-authentication or return a TGT (if pre-authentication is not required).
3. If the username is invalid:
   - The KDC responds with `PRINCIPAL UNKNOWN`.
#### Steps to Enumerate Usernames:

1. **Prepare a Username List:**
   - Clean and deduplicate a list of usernames.
```bash
cat /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt | tr '[:upper:]' '[:lower:]' | sort -u > kerberos_users.txt
```

2. **Use Kerbrute to Enumerate Valid Usernames:**
   - Test the usernames against the KDC.
```bash
kerbrute userenum -d domain.tld --dc dc-ip-here -t 100 -o kerbrute.log ./kerberos_users.txt
```

3. **Extract Valid Usernames:**
   - Extract valid usernames from the Kerbrute log.
```bash
cat kerbrute.log | grep '@' kerbrute.log | awk -v FS=' ' '{print $7}' | cut -d '@' -f 1 > as_rep_test.txt
```

4. **Find AS-REP Hashes (for Users Without Pre-Auth):**
   - Use Impacket's `GetNPUsers` to extract AS-REP hashes for users who don’t require pre-authentication.

#### Using Nmap for Enumeration:
- Nmap can also enumerate usernames but is slower and less efficient for large lists.
- Example command:
```bash
domain_controller=dc1.domain.tld
domain='domain.tld'
username_list='/usr/share/seclists/Usernames/top-usernames-shortlist.txt'
nmap -Pn -p88 --script krb5-enum-users --script-args krb5-enum-users.realm=$domain,userdb=$username_list $domain_controller
```



# Step 2.1 Service Enum: General
#### File servers — FTP/SMB
- May allow anonymous access or may be configured with default credentials
- This is an _****excellent opportunity****_ to gather more information from files
- Additional information may include usernames, passwords, config files, etc.
- This information maybe useful when assessing other services
#### Web — HTTP/HTTPS
- Web is just as simple as opening your web browser
- Navigate to the target IP or domain name and just start clicking around
- Make a note of potential input points that could be abused
- Web pages may contain usernames, passwords, interesting source code, etc.
#### other things
- Start probing other ports, try to understand how they behave
- Lots of Googling, probably something on HackTricks about it
- Try other `nmap` scans to see if additional ports are revealed; UDP or X-Mas scans, for example.

## FTP — TCP/21
- Check for anonymous FTP access on the target
```bash
ftp anonymous@10.10.100.44
```
When prompted for a password, simply press the `Enter` key and see if it will allow you to login. If it does, try the following:
- `ls` to list files on the server
- `get` to retrieve files on the server
- `less` or `more` to read files from the FTP shell
- `cd` to change into any potential directories
- `put` to test write permissions as a way to perhaps chain an exploit with another service
We're trying to uncover (Usernames Passwords Configuration files Source code Backups Anything interesting)
## SMB — TCP/139 & TCP/445
