# Authentication Vulnerabilities

Authentication vulnerabilities occur when weaknesses in an application's login, password management, session verification, or multi-factor authentication mechanisms allow attackers to compromise user accounts.

This repository documents my practical exercises from PortSwigger Web Security Academy, focusing on identifying, exploiting, and understanding common authentication weaknesses.

## Labs Covered

## Labs Covered

| # | Lab | Key Technique |
|---|---|---|
| 1 | [Username Enumeration via Different Responses](./Username_enumeration_via_different_responses.md) | Identifying valid usernames through differences in server responses |
| 2 | [Username Enumeration via Subtly Different Responses](./Username_enumeration_via_subtly_different_responses.md) | Detecting subtle response differences using Grep - Extract |
| 3 | [Username Enumeration via Response Timing](./Username_enumeration_via_response_timing.md) | Exploiting response-time differences to enumerate usernames |
| 4 | [Broken Brute-Force Protection, IP Block](./Broken_brute-force_protection_IP_block.md) | Bypassing counter-based brute-force protection |
| 5 | [2FA Simple Bypass](./2FA_simple_bypass.md) | Bypassing two-factor authentication through flawed access control |
| 6 | [2FA Broken Logic](./2FA_broken_logic.md) | Exploiting weaknesses in two-factor authentication logic |
| 7 | [Brute-Forcing a Stay-Logged-In Cookie](./Brute-forcing_a_stay-logged-in_cookie.md) | Cracking a predictable persistent authentication cookie |
| 8 | [Offline Password Cracking](./Offline_password_cracking.md) | Stealing and cracking password hashes through XSS |
| 9 | [Password Reset Broken Logic](./Password_reset_broken_logic.md) | Exploiting improper account binding in password reset functionality |
| 10 | [Password Reset Poisoning via Middleware](./Password_reset_poisoning_via_middleware.md) | Manipulating forwarded headers to poison password reset links |
| 11 | [Password Brute-Force via Password Change](./Password_brute-force_via_password_change.md) | Exploiting password-change response differences to identify valid passwords |

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