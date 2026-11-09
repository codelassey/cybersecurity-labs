# Authentication Vulnerabilities

Authentication vulnerabilities occur when weaknesses in an application's login, password management, session verification, or multi-factor authentication mechanisms allow attackers to compromise user accounts.

This repository documents my practical exercises from PortSwigger Web Security Academy, focusing on identifying, exploiting, and understanding common authentication weaknesses.

## Labs Covered

| # | Lab | Key Vulnerability |
|---|---|---|
| 1 | [Username Enumeration via Different Responses](./username-enumeration-via-different-responses.md) | Identifying valid usernames through inconsistent error messages |
| 2 | [Username Enumeration via Subtly Different Responses](./username-enumeration-via-subtly-different-responses.md) | Detecting subtle response differences using Grep - Extract |
| 3 | [Username Enumeration via Response Timing](./username-enumeration-via-response-timing.md) | Exploiting response-time differences to enumerate usernames |
| 4 | [Broken Brute-Force Protection, IP Block](./broken-brute-force-protection-ip-block.md) | Bypassing brute-force protection through authentication logic flaws |
| 5 | [2FA Simple Bypass](./2fa-simple-bypass.md) | Accessing an account without completing the second authentication step |
| 6 | [2FA Broken Logic](./2fa-broken-logic.md) | Exploiting flaws in multi-factor authentication verification |
| 7 | [Brute-Forcing a Stay-Logged-In Cookie](./brute-forcing-a-stay-logged-in-cookie.md) | Recovering passwords from predictable authentication cookies |
| 8 | [Offline Password Cracking](./offline-password-cracking.md) | Combining stored XSS and weak cookie construction to recover a password |
| 9 | [Password Reset Broken Logic](./password-reset-broken-logic.md) | Reusing a password-reset token to change another user's password |
| 10 | [Password Reset Poisoning via Middleware](./password-reset-poisoning-via-middleware.md) | Manipulating `X-Forwarded-Host` to capture password-reset tokens |
| 11 | [Password Brute-Force via Password Change](./password-brute-force-via-password-change.md) | Exploiting password-change responses to brute-force credentials |

## Key Takeaways

Through these labs, I explored how authentication weaknesses can arise from:

- Inconsistent login responses and response-time differences.
- Weak brute-force protection and inadequate rate limiting.
- Improperly implemented multi-factor authentication.
- Predictable or insecurely constructed authentication cookies.
- Password-reset workflows that fail to properly validate tokens.
- Trusting unvalidated HTTP headers when generating password-reset links.
- Information leakage through password-change functionality.
- Subtle differences in authentication error messages, including punctuation inconsistencies, can expose valid usernames.
- Burp Suite's Grep - Extract feature can help automate the identification of response differences during enumeration.

## How to Secure Your Authentication Mechanisms

### 1. Protect User Credentials

- Never transmit login credentials over unencrypted connections.
- Enforce HTTPS by redirecting HTTP requests to secure connections.
- Audit applications to prevent unintended disclosure of usernames and email addresses through public profiles or HTTP responses.

### 2. Don't Rely on Users for Security

- Implement effective password policies.
- Use password-strength checkers such as `zxcvbn` to help users create stronger passwords.
- Provide real-time feedback on password strength rather than relying solely on traditional complexity requirements.

### 3. Prevent Username Enumeration

- Use identical, generic error messages for valid and invalid usernames.
- Return consistent HTTP status codes across login attempts.
- Minimise response-time differences that could reveal whether an account exists.

### 4. Implement Robust Brute-Force Protection

- Enforce effective IP-based rate limiting.
- Prevent attackers from bypassing rate limits by manipulating their apparent IP address.
- Introduce CAPTCHA challenges after a defined number of failed login attempts.
- Monitor repeated authentication failures and suspicious login activity.

### 5. Triple-Check Verification Logic

- Thoroughly audit authentication and verification logic.
- Ensure that security checks cannot be bypassed by manipulating request parameters.
- Validate that reset tokens, usernames, sessions, and authentication states are correctly associated.
- Ensure that every step of a multi-step authentication process enforces the required checks.

### 6. Secure Supplementary Authentication Functionality

Do not focus exclusively on the main login page. Password-reset and password-change functionality are equally important attack surfaces.

- Bind password-reset tokens to the intended user account.
- Use secure, unpredictable, short-lived, single-use reset tokens.
- Avoid trusting user-supplied usernames or HTTP headers when determining account identity or generating reset links.
- Apply brute-force protection to password-change and password-reset endpoints.
- Secure authentication cookies against theft and manipulation.

### 7. Implement Proper Multi-Factor Authentication

- Use properly implemented MFA rather than relying on password-based authentication alone.
- Prefer authenticator applications or dedicated security devices over less secure verification methods.
- Understand that email-based verification is not true independent multi-factor authentication.
- Be cautious with SMS-based verification because of risks such as SIM swapping.
- Ensure that every protected endpoint verifies that the user has successfully completed all required authentication steps.

## Final Thoughts

These exercises reinforced an important principle: authentication security is not limited to validating a username and password. Every supporting mechanism, including session management, password changes, password resets, and multi-factor authentication, must enforce the same level of security.

A weakness in just one of these mechanisms can undermine the entire authentication system.