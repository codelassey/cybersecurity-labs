# PortSwigger Web Security Academy - Access control vulnerabilities and privilege escalation

A concise README documenting access control vulnerabilities, exploitation techniques, HTTP request manipulation, and key security lessons learned while completing PortSwigger Web Security Academy labs.

## Table of Contents

1. [Unprotected Admin Functionality](#1-unprotected-admin-functionality)
2. [Unprotected Admin Functionality with Unpredictable URL](#2-unprotected-admin-functionality-with-unpredictable-url)
3. [User Role Controlled by Request Parameter](#3-user-role-controlled-by-request-parameter)
4. [User Role Can Be Modified in User Profile](#4-user-role-can-be-modified-in-user-profile)
5. [URL-Based Access Control Can Be Circumvented](#5-url-based-access-control-can-be-circumvented)
6. [Method-Based Access Control Can Be Circumvented](#6-method-based-access-control-can-be-circumvented)
7. [User ID Controlled by Request Parameter](#7-user-id-controlled-by-request-parameter)
8. [User ID Controlled by Request Parameter with Unpredictable User IDs](#8-user-id-controlled-by-request-parameter-with-unpredictable-user-ids)
9. [User ID Controlled by Request Parameter with Data Leakage in Redirect](#9-user-id-controlled-by-request-parameter-with-data-leakage-in-redirect)
10. [User ID Controlled by Request Parameter with Password Disclosure](#10-user-id-controlled-by-request-parameter-with-password-disclosure)
11. [Insecure Direct Object References (IDOR)](#11-insecure-direct-object-references-idor)
12. [Multi-Step Process with No Access Control on One Step](#12-multi-step-process-with-no-access-control-on-one-step)
13. [Referer-Based Access Control](#13-referer-based-access-control)
14. [How to Prevent Access Control Vulnerabilities](#how-to-prevent-access-control-vulnerabilities)

---

## Labs Covered

| # | Lab | Write-up |
|---:|---|---|
| 1 | Unprotected Admin Functionality | [View Write-up](Unprotected_admin_functionality.md) |
| 2 | Unprotected Admin Functionality with Unpredictable URL | [View Write-up](Unprotected_admin_functionality_with_unpredictable_URL.md) |
| 3 | User Role Controlled by Request Parameter | [View Write-up](User_role_controlled_by_request_parameter.md) |
| 4 | User Role Can Be Modified in User Profile | [View Write-up](User_role_can_be_modified_in_user_profile.md) |
| 5 | URL-Based Access Control Can Be Circumvented | [View Write-up](URL-based_access_control_can_be_circumvented.md) |
| 6 | Method-Based Access Control Can Be Circumvented | [View Write-up](Method-based_access_control_can_be_circumvented.md) |
| 7 | User ID Controlled by Request Parameter | [View Write-up](User_ID_controlled_by_request_parameter.md) |
| 8 | User ID Controlled by Request Parameter with Unpredictable User IDs | [View Write-up](User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs.md) |
| 9 | User ID Controlled by Request Parameter with Data Leakage in Redirect | [View Write-up](User_ID_controlled_by_request_parameter_with_data_leakage_in_redirect.md) |
| 10 | User ID Controlled by Request Parameter with Password Disclosure | [View Write-up](User_ID_controlled_by_request_parameter_with_password_disclosure.md) |
| 11 | Insecure Direct Object References (IDOR) | [View Write-up](Insecure_direct_object_references.md) |
| 12 | Multi-Step Process with No Access Control on One Step | [View Write-up](Multi-step_process_with_no_access_control_on_one_step.md) |
| 13 | Referer-Based Access Control | [View Write-up](Referer-based_access_control.md) |

**Total labs documented:** 13

---

## 1. Unprotected Admin Functionality

**Vulnerability:** Administrative functionality is accessible without proper authentication or authorization.

**Exploitation:**
1. Inspect `robots.txt` for potentially exposed administrative paths.
2. Identify the admin panel URL.
3. Navigate directly to the endpoint.
4. Check whether administrative actions can be performed without authorization.

**Key takeaway:** Hiding an administrative URL does not protect it.

## 2. Unprotected Admin Functionality with Unpredictable URL

**Vulnerability:** An administrative URL is difficult to guess but exposed through client-side code.

**Exploitation:**
1. Inspect publicly accessible JavaScript and HTML.
2. Search for hidden administrative paths.
3. Identify the admin URL.
4. Navigate directly to the endpoint and test whether server-side authorization is enforced.

**Key takeaway:** Client-side code is accessible to users and must never be treated as a security boundary.

## 3. User Role Controlled by Request Parameter

**Vulnerability:** The application trusts a client-controlled cookie or request parameter to determine user privileges.

**Exploitation:**
1. Log in with a regular user account.
2. Inspect session cookies and HTTP requests.
3. Identify a role indicator, such as `Admin=false`.
4. Modify the value to `Admin=true`.
5. Resend the request and check whether administrative functionality becomes accessible.

**Key takeaway:** Roles and permissions must be determined and validated server-side.

## 4. User Role Can Be Modified in User Profile

**Vulnerability:** The application accepts unauthorized changes to privileged account attributes through a profile update.

**Exploitation:**
1. Log in with a regular user account.
2. Inspect profile update requests and responses.
3. Identify unexpected fields, such as `roleid`.
4. Test whether unauthorized role modifications are accepted.
5. Verify whether the account gains additional privileges.

**Key takeaway:** Restrict profile updates to explicitly permitted fields. Users must not be allowed to assign themselves roles.

## 5. URL-Based Access Control Can Be Circumvented

**Vulnerability:** Access restrictions rely on front-end URL filtering or can be bypassed by manipulating URL-related headers.

**Exploitation:**
1. Attempt to access a restricted administrative endpoint.
2. Inspect the HTTP response and identify URL rewriting behavior.
3. Test whether headers such as `X-Original-URL` influence the endpoint processed by the backend.
4. Determine whether URL rewriting bypasses front-end restrictions.
5. Verify whether the backend independently enforces authorization.

**Example header:**

```http
X-Original-URL: /admin
```

**Key takeaway:** Front-end restrictions and URL rewriting must not replace backend authorization checks.

## 6. Method-Based Access Control Can Be Circumvented

**Vulnerability:** Authorization checks depend on the HTTP method used to perform an action.

**Exploitation:**
1. Observe an administrative action performed through a privileged session.
2. Send the request to Burp Suite Repeater.
3. Replace the privileged session with a regular user's session.
4. Test whether changing the HTTP method bypasses the restriction.
5. Verify whether the action succeeds without the required privileges.

**Key takeaway:** Authorization must be enforced consistently across HTTP methods and equivalent endpoints.

## 7. User ID Controlled by Request Parameter

**Vulnerability:** The application retrieves account information using a user-controlled identifier without checking ownership.

**Exploitation:**
1. Log in with a regular account.
2. Inspect the account request.

   ```http
   GET /my-account?id=wiener HTTP/1.1
   ```

3. Change the identifier to another user's ID.

   ```http
   GET /my-account?id=carlos HTTP/1.1
   ```

4. Inspect the response for unauthorized account information, such as an API key.

**Key takeaway:** Every account-specific request must verify that the authenticated user is authorized to access the requested account.

## 8. User ID Controlled by Request Parameter with Unpredictable User IDs

**Vulnerability:** The application uses unpredictable identifiers but exposes them through accessible resources and fails to enforce authorization.

**Exploitation:**
1. Browse accessible content, such as blog posts.
2. Inspect HTTP responses for user GUIDs or other identifiers.
3. Identify a resource associated with the target user.
4. Extract the user's GUID from the response.
5. Substitute that GUID into the account request and test for unauthorized access.

**Key takeaway:** Unpredictable identifiers do not replace authorization. GUIDs can still be discovered through application responses.

## 9. User ID Controlled by Request Parameter with Data Leakage in Redirect

**Vulnerability:** Sensitive information is included in the body of an HTTP redirect response.

**Exploitation:**
1. Capture an account request in Burp Suite Repeater.
2. Change the user ID to another account.
3. Inspect the complete HTTP response, including the body of any `302 Found` response.
4. Check whether sensitive information, such as an API key, is exposed.
5. Verify the findings without relying solely on what the browser displays.

**Key takeaway:** A redirect does not erase or protect sensitive information already included in the original response.

## 10. User ID Controlled by Request Parameter with Password Disclosure

**Vulnerability:** The application exposes existing passwords in account responses and permits unauthorized access to other users' account information.

**Exploitation:**
1. Inspect the account response for plaintext password values.
2. Modify the user ID parameter to test for horizontal privilege escalation.
3. Identify whether another user's password is disclosed.
4. Within the authorized lab, test the discovered credentials against the relevant account.
5. Assess whether access to a privileged account enables further administrative actions.

**Key takeaway:** Password masking is only a visual presentation feature. Passwords must never be retrievable in plaintext from application responses.

## 11. Insecure Direct Object References (IDOR)

**Vulnerability:** The application retrieves server-side files using user-controlled identifiers without appropriate authorization checks.

**Exploitation:**
1. Open the application's live chat feature.
2. Download a transcript and identify its filename.
3. Inspect HTTP history to find the download endpoint.
4. Change the filename in the URL to access another transcript.
5. Inspect the retrieved file for exposed credentials.
6. Use the credentials to authenticate to the relevant account if required by the lab objective.

**Example request:**

```http
GET /download-transcript/2.txt HTTP/1.1
```

**Key takeaway:** File identifiers must not grant access to resources without authorization. Sensitive credentials should never be stored in retrievable chat transcripts.

## 12. Multi-Step Process with No Access Control on One Step

**Vulnerability:** A sensitive multi-step operation enforces authorization at one stage but omits it at another.

**Exploitation:**
1. Use an administrator account to observe a role-change workflow.
2. Capture the initial request and final confirmation request.
3. Identify parameters such as `username`, `action`, and `confirmed`.
4. Send the final request to Burp Suite Repeater.
5. Replace the administrator's session with a regular user's session.
6. Change the target username to the regular user.
7. Resend the request and verify whether the role change succeeds.

**Example parameters:**

```http
username=wiener&action=upgrade&confirmed=true
```

**Key takeaway:** Every step of a sensitive workflow must independently enforce authorization. A confirmation parameter is not proof of permission.

## 13. Referer-Based Access Control

**Vulnerability:** The application relies on the HTTP `Referer` header to determine whether a user can perform an administrative action.

**Exploitation:**
1. Use an administrator account to capture a role-upgrade request.
2. Identify the `Referer` header and its value.
3. Forward the request to Burp Suite Repeater.
4. Replace the administrator's session with a regular user's session.
5. Change the target username to the regular user.
6. Set the `Referer` header to the administrative page.
7. Send the request and verify whether the account's privileges change.

**Example header:**

```http
Referer: https://example.com/admin
```

**Key takeaway:** The `Referer` header is client-controlled metadata and must never be used as an authorization mechanism.

---

## Common Testing Workflow

When assessing access control in a web application, use this checklist:

1. **Map the application:** Identify account pages, administrative endpoints, file downloads, and sensitive workflows.
2. **Capture requests:** Use Burp Suite Proxy and HTTP history to understand how the application handles requests.
3. **Identify trust boundaries:** Examine cookies, query parameters, request bodies, HTTP methods, and headers.
4. **Test horizontal access:** Determine whether one user can access another user's data.
5. **Test vertical access:** Determine whether a regular user can perform administrator-only actions.
6. **Test alternate paths:** Check direct URLs, rewritten URLs, redirects, and multi-step workflows.
7. **Verify responses:** Inspect HTTP status codes, response bodies, redirects, and resulting application state.
8. **Document findings:** Record the vulnerable endpoint, required conditions, impact, and recommended remediation.

## Key Takeaways

| Attack Pattern | Core Weakness |
|---|---|
| Hidden admin URL | Security through obscurity |
| Modified role cookie | Client-controlled authorization |
| Profile role modification | Mass assignment |
| URL rewriting | Inconsistent access enforcement |
| HTTP method manipulation | Method-dependent authorization |
| Modified user ID or GUID | IDOR / broken object-level authorization |
| Sensitive redirect body | Information disclosure |
| Password exposure | Insecure handling of credentials |
| Modified transcript filename | Unauthorized file access |
| Missing workflow-step authorization | Incomplete authorization checks |
| Forged `Referer` header | Trust in client-controlled metadata |

---

## How to prevent access control vulnerabilities

Access control vulnerabilities can be prevented by taking a defense-in-depth approach and applying the following principles:

- Never rely on obfuscation alone for access control.
- Unless a resource is intended to be publicly accessible, deny access by default.
- Wherever possible, use a single application-wide mechanism for enforcing access controls.
- At the code level, make it mandatory for developers to declare the access that is allowed for each resource, and deny access by default.
- Thoroughly audit and test access controls to ensure they work as designed.