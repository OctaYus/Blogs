## What is Privilege Escalation?

Privilege escalation occurs when a user gains access to permissions or roles beyond their intended authorization. This typically involves escalating from a low-privileged user (e.g., "ViewOnly" or guest) to a high-privileged role (e.g., admin or superuser). Such vulnerabilities can result in full account compromise, unauthorized data access, or critical application control.

---

## Vulnerability Overview

### Application Context

* The target application is a **social media management platform**.
* It uses **JWT (JSON Web Tokens)** for authentication and authorization.
* The backend does **not validate whether the user is authorized** to perform privileged actions.

### Root Cause

The API accepts any JWT with a valid structure, but **does not perform proper authorization checks** on sensitive endpoints. This means a user can craft requests that impersonate higher roles (such as "admin") even if their actual token is for a lower-privileged account.

### Impact

This flaw allows a low-privileged user (such as "ViewOnly") to escalate their privileges and perform admin-level actions—such as modifying roles, editing user details, or managing team settings—without being authorized.

---

## Steps to Reproduce (Privilege Escalation from ViewOnly to Admin)

1. Set up an HTTP interception proxy like **Burp Suite** or **OWASP ZAP**.
2. Log into two accounts in the application:

   * One with **Admin** privileges
   * One with **ViewOnly** privileges
3. From the **Admin account**, navigate to the endpoint:
   `https://www.redacted.com/settings/#team`
4. Enable interception in Burp Suite and capture the request where the Admin modifies another user's role.
5. Send this request to Burp's **Repeater**.
6. Replace the **Authorization: Bearer** token with the JWT belonging to the **ViewOnly** account.
7. In the request body, set the `"role"` field to `"admin"`.
8. Send the request.

**Result:**
The request is accepted, and the ViewOnly user's role is updated to Admin—even though the user does not have permission to perform this action.

---

## Security Impact

This vulnerability allows any authenticated user to:

* Escalate their role to Admin
* Modify or manage other user accounts
* Potentially gain full control over organization data

