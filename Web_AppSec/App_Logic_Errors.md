## MFA

### simple scenario
the App use the MFA to login users using multiple ways not just the password so let's  assume a normal authentication flow would look like this:
Step 1 (Password Check) -> Step 2 (MFA) -> Step 3 (Security Questions)
- step 1 the user will provide the pass at `https://example.com/login/`
- step 2 to provide the MFA code at `https://example.com/mfa/` 
- step 3 security question at `https://example.com/security_questions/` 
what if u manipulate the URL and access the `https://example.com/security_questions/` directly without providing the MFA code? if it worked then it's a broken logic and access control

