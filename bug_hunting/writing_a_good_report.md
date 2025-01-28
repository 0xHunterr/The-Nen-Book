> **In this section, I’ll break down the components of a good vulnerability report and introduce some tips and tricks I’ve learned along the way

## Step 1: Craft a Descriptive Title

- Aim for a title that sums up the issue in one sentence. Ideally, it should allow the
security team to immediately get an idea of what the vulnerability is, where it occurred, and its potential severity. To do so, it should answer the following questions:(**What is the vulnerability you’ve found? Is it an instance of a well-known vulnerability type, such as IDOR or XSS? Where did you find it on the target application?**)

Example: instead of a report title like “IDOR on a Critical Endpoint,”
use one like “**IDOR on `https://example.com/change_password` Leads to Account Takeover for All Users**.” 
- the goal is to give the security engineer reading
your report a good idea of the content you’ll discuss in the rest of it.

## Step 2: Provide a Clear Summary
This section includes all the relevant details you weren’t able to communicate in the title, like the HTTP request parameters used for the attack, how you found it, and so on.
Example: The `https://example.com/change_password` endpoint takes two POST body parameters: `user_id` and `new_password`.
A POST request to this endpoint would change the password of user `user_id` to `new_password`. 
This endpoint is not validating the `user_id` parameter, and as a result, any user can change anyone else’s password by manipulating the `user_id` parameter.
It contains all the information needed to understand a vulnerability, including what the bug is, where the bug is found, and what an attacker can do when it’s exploited
## Step 3: Include a Severity Assessment and CVSS
Your report should also include an honest assessment of the bug’s severity.
You could use the following scale to communicate severity:

- **Low severity:** The bug doesn’t have the potential to cause a lot of damage.
  ex: an open redirect that can be used only for phishing is a low-severity bug.

- **Medium severity:** The bug impacts users or the organization in a moderate way, or is a high-severity issue that’s difficult for a malicious hacker to exploit.
  ex: a cross-site request forgery (CSRF) on a sensitive action such as password change is often considered a medium-severity issue.
 
- **High severity**: The bug impacts a large number of users, and its consequences can be disastrous for these users.
  ex: an open redirect that can be used to steal OAuth tokens is a high-severity bug
  
- **Critical severity"**: The bug impacts a majority of the user base or endangers the organization’s core infrastructure.
  ex: SQL injection leading to remote code execution (RCE) on the production server will be considered a critical issue.
### CVSS

- The CVSS scale takes into account factors such as how a vulnerability impacts an organization, how hard the vulnerability is to exploit, and whether the vulnerability requires any special privileges or user interaction to exploit.
- study [CVSS](https://www.first.org/cvss/)
- Customize your assessment to fit the client’s business priorities for example:

ex: a dating site might find a bug that exposes a user’s birth date as inconsequential, since
a user’s age is already public information on the site. On the other hand, leaks of users’
banking information are almost always considered a high-severity issue.

- use the rating scale of a bug bounty platform. For example, Bugcrowd’s rating system takes into account the type of vulnerability and the affected functionality [Bugcrowd’s Vulnerability Rating Taxonomy - Bugcrowd](https://bugcrowd.com/vulnerability-rating-taxonomy/), and HackerOne provides a severity calculator based on the CVSS scale [Severity | HackerOne Help Center](https://docs.hackerone.com/en/articles/8475343-severity)
- You could list the severity in a single line, as follows: Severity of the issue: High

## Step 4: Give Clear Steps to Reproduce
It’s best to assume the engineer on the other side has no knowledge of the vulnerability and doesn’t know how the application works. For example :

1. Make two accounts on `example.com`: account A and account B.
2. Log in to `example.com` as account A, and visit `https://example.com/change_password`.
3. Fill in the desired new password in the New password field, located at the top left of the page.
4. Click the Change Password button located at the top right of the page.
5. Intercept the POST request to `https://example.com/change_password` and change the `user_id` POST parameter to the `user ID` of account B.
6. You can now log in to account B by using the new password you’ve chosen.
## Step 5: Provide a Proof of Concept
But for more complex vulnerabilities, it’s helpful to include a video, screenshots, or photos documenting your exploit, called a proof-of-concept (POC) file.

For example, for a CSRF vulnerability, you could include an HTML file with the CSRF payload embedded. And for vulnerabilities that require multiple complicated
steps to reproduce, you could film a screen-capture video of you walking through the process.
also include any crafted URLs, scripts, or upload files you used to attack the application.

## Step 6: Describe the Impact and Attack Scenarios
To help the security team fully understand the potential impact of the vulnerability, you can also illustrate a plausible scenario in which the vulnerability could be exploited. 
Note that this section is not the same as the severity assessment I mentioned earlier

**The severity assessment describes** the severity of the consequences of an attacker exploiting the vulnerability, whereas **the attack scenario** explains what those consequences would actually look like.
If hackers exploited this bug, could they take over user accounts?

Put yourself in a malicious hacker’s shoes and try to escalate the impact of the vulnerability as much as possible. Give the client company a realistic sense
of the worst-case scenario. A good impact section illustrates how an attacker can realistically exploit a bug

For example: Using this vulnerability, all that an attacker needs in order to change a user’s password is their user_id. 
Since each user’s public profile page lists the account’s user_id, anyone can visit any user’s profile, find out their user_id, and change their password. And because user_ids are simply sequential numbers, a hacker can even enumerate all the user_ids and change the passwords of all users! This bug will let attackers take over anyone’s account with minimal effort.

## Step 7: Recommend Possible Mitigations
since you’re the security researcher who discovered the vulnerability, you’ll be familiar with the particular behavior of that application feature

If you’re not sure what caused the vulnerability or what a possible fix might be, avoid giving any recommendations so you don’t confuse your reader.

And You don’t have to go into the technical details of the fix, since you
don’t have knowledge of the application’s underlying codebase.

For Example: The application should validate the user’s user_id parameter within the change password request to ensure that the user is authorized to make account modifications. Unauthorized requests should be rejected and logged by the application.

## Step 8: Validate the Report
Follow your own Steps to Reproduce to ensure that they contain enough details.
Examine all of you POC files and code to make sure they work.

# Additional Tips for Writing Better Reports
- **Don’t Assume Anything:** don’t assume that the security team will be able to understand everything in your report. Remember that you might have been working with
this vulnerability for a week, but to the security team receiving the report, it’s all new info Be as verbose as possible, and include all the relevant details you can think of. It’s also good to include links to references.
- **Be Clear and Concise:** don’t include any unnecessary information, such as wordy greetings, jokes, or memes. A security report is a business document Write in a conversational tone and don’t use leetspeak, slang, or abbreviations.

# Understanding Report States
**Need More Information:** This means the security team didn’t fully understand your report, or couldn’t reproduce the issue by using the information you’ve provided.in this case, you should revise your report, provide any missing information, and address the security team’s additional concerns

**Informative:** This means they believe the issue you reported is a security concern but
not significant enough to warrant a fix. In this case, there’s nothing more you can do for the report! The company won’t pay you a bounty However, I do recommend that you keep track
of informative issues and try to chain them into bigger, more impactful bugs.

**Duplicate:** means another hacker has already found the bug There’s nothing more to do with the report, u can also try to escalate or chain the bug into a more impactful bug

**not applicable(N/A):** means your report doesn’t contain a valid security issue with security implications. This might happen when your report contains technical errors, or if the bug is intentional application behavior

**Triaged:** This is great news for you, because this usually means the security team is going to fix the bug and reward you with a bounty. Once the report has been triaged, you should help the security team fix the issue.

**Resolved:** the reported vulnerability has been fixed. expect to receive your payment at this point!
