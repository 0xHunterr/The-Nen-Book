# Asset Types:
In the context of a bug bounty program, an asset is an application, website,
or product that you can hack. There are different types of assets, each with
its own characteristics, requirements, and pros and cons
## Social Sites and Applications:
Anything labeled social has a lot of potential for vulnerabilities, because
these applications tend to be complex and involve a lot of interaction among
users, and between the user and the server

Social applications need to manage interactions among users, as well as each user’s roles, privileges, and account integrity.
They are typically full of potential for critical web vulnerabilities such as insecure direct object references (IDORs), info leaks, and account takeovers. 
These vulnerabilities occur when many users are on a platform, and when applications mismanage user information; when the application does not validate a user’s identity properly, malicious users can assume the identity of others.
These complex applications also often provide a lot of user input opportunities. If input validation is not performed properly, these applications are prone to injection bugs, like SQL injection (SQLi) or cross-site
scripting (XSS).
==If you are a newcomer to bug bounties, I recommend that you start with social sites.==

The skill set you need to hack social programs includes the ability to
use a proxy, like the Burp Suite proxy, and knowledge about web vulnerabilities such as XSS and IDOR.. It’s also helpful to have some **JavaScript** programming skills and knowledge about web development.

## General Web Applications

Here, I am referring to any web applications that do not involve user-to-user interaction.
Instead, users interact with the server to access the application’s features.
These targets can include static websites, cloud applications, consumer services like banking sites, and web portals of Internet of Things (IoT) devices or other connected hardware. Like social sites, they are also quite diverse and lend themselves well to a variety of skill levels.
Examples include the programs for Google, the US Department of Defense

That said, in my experience, they tend to be a little more difficult to hack than social applications, and their attack surface is smaller. 
If you’re looking for account takeovers and info leak vulnerabilities

**the types of vulns u need to look for:** you’ll find in these applications are slightly different. You’ll need to look for server-side vulnerabilities and vulnerabilities specific to the application’s technology stack.
also look for commonly found network vulnerabilities, like subdomain takeovers. This means you’ll have to know about both client-side and server-side web vulnerabilities
## Mobile Applications (Android, iOS, and Windows)

After you get the hang of hacking web applications, you may choose to specialize in mobile applications. Mobile programs are becoming prevalent; after all, most web apps have a mobile equivalent nowadays.
requires the skill set you’ve built from hacking web applications, as well as additional knowledge about the structure of mobile apps and programming techniques related to the platform.
You should understand attacks and analysis strategies like certificate pinning bypass, mobile reverse engineering, and cryptography.
Hacking mobile applications also requires a little more setup than hacking web applications, as you’ll need to own a mobile device that you can experiment on. 
A good mobile testing lab consists of a regular device, a rooted device, and device emulators for both Android and iOS. These programs are less competitive, making it relatively easy to find bugs.

## **APIs**

Application programming interfaces (APIs) are specifications that define how other applications can interact with an organization’s assets, such as to retrieve or alter their data. A secure API implementation is key to preventing data breaches and protecting customer data.
Hacking APIs requires many of the same skills as hacking web applications, mobile applications, and IoT applications. 
But when testing APIs, you should focus on common API bugs like data leaks and injection flaws.

## Source Code and Executables

If you have more advanced programming and reversing skills, you can give
source code and executable programs a try. These programs encourage hackers
to find vulnerabilities in an organization’s software by directly providing
hackers with an open source codebase or the binary executable. Examples
include the Internet Bug Bounty, the program for the PHP language, and
the WordPress program.

Hacking these programs can entail analyzing the source code of open source projects for web vulnerabilities and fuzzing binaries for potential exploits. 
You usually have to understand coding and computer science concepts to be successful here. You’ll need knowledge of web vulnerabilities, programming skills related to the project’s codebase, and code analysis
skills. 
Cryptography, software development, and reverse engineering skills are helpful. 

You don’t have to be a master programmer to hack these programs; rather, aim for a solid understanding of the project’s tech stack and underlying architecture. 
Because these programs tend to require more skills, they are less competitive, and only a small proportion of hackers will ever attempt them.

## Hardware and IoT
Last but not least are hardware and IoT programs. These programs ask you to hack devices like cars, smart televisions, and thermostats. Examples include the bug bounty programs of Tesla and Ford Motor Company

You’ll need highly specific skills to hack these programs: you’ll often have to acquire a deep familiarity with the type of device that you’re hacking, in addition to understanding common IoT vulnerabilities. 
You should know about web vulnerabilities, programming, code analysis, and reverse engineering. Also, study up on IoT concepts and industry standards such as digital signing and asymmetric encryption schemes. Finally, cryptography, wireless hacking, and software development skills will be helpful too.
Since these programs require specialized skills and a device, they tend to be the least competitive

# As a bug bounty hunter, should you hack on a bug bounty platform? Or

should you go for companies’ independently hosted programs?
**The Pros . . .**
The best thing about bug bounty platforms is that they provide a lot of transparency into a company’s process, because they post disclosed reports,
metrics about the programs’ triage rates, payout amounts, and response times. Independently hosted programs often lack this type of transparency.
In the bug bounty world, triage refers to the confirmation of vulnerability.
You also won’t have to worry about the logistics of emailing security teams, following up on reports, and providing payment and tax info every time you submit a vulnerability report. Bug bounty programs also often have reputation systems that allow you to showcase your experience so you can gain access to invite-only bug bounty programs.

**. . . and the Cons**
Sometimes Reports submitted to platform managed bug bounty programs often get handled by triagers, third-party employees who often aren’t familiar with all the security.

Complaints about triagers handling reports improperly are common.
Programs on platforms also break the direct connection between hackers and developers.
Finally, public programs on bug bounty platforms are often crowded, because the platform gives them extra exposure. On the other hand, many privately hosted programs don’t get as much attention from hackers and are thus less competitive.
## How to get an Invitation to Private programs?

> **To get private invites, you often need to gain a certain number of reputation points on a platform u can get them by:**

- submit a few bugs to public programs.
- You should also focus on submitting high-impact vulnerabilities.
- solving Capture the Flag (CTF) challenges on some platforms like H1
- Next, don’t spam. Submitting nonissues often causes a decrease in reputation points.
- Finally, be polite and courteous when communicating with security teams. 
Being rude or abusive to security teams will probably get you banned
from the program and prevent you from getting private invites from other
companies

# Choosing the Right Program?

- it’s becoming increasingly difficult for beginners to get started.
	That’s why it’s important to pick a program that you can succeed in from
	the very start.
- Before you develop a bug hunter’s intuition, you often have to rely on low-hanging fruit and well-known techniques.
- pick a program that more experienced bug hunters pass over to avoid competition and u can do it in 2 ways: look for unpaid programs **or** go for programs with big scopes.
- Try going for vulnerability disclosure programs first.
- Go for programs with fast response times to prevent frustration and get feedback as soon as possible.

After you’ve identified a few programs that you are interested in, you could
list the properties of each one to compare them

![Untitled](../Media/Web%20AppSec%20Images/Untitled.png)

