# Lab Report: User ID Controlled by Request Parameter

## Lab Overview

This PortSwigger Web Security Academy apprentice lab demonstrated a horizontal privilege escalation vulnerability caused by an insecure direct object reference (IDOR). The objective was to obtain the API key belonging to another user, `carlos`, and submit it as the lab solution.

## Tools Used

- Burp Suite
- Burp Proxy
- Burp Repeater
- PortSwigger Web Security Academy browser

## Exploitation Steps

1. Logged in to the application using the provided user account credentials:

   ```text
   wiener:peter
   ```

2. Navigated to the **My account** page.

3. Captured the request sent to load the account page in Burp Proxy.

4. Sent the captured request to **Repeater**.

5. Identified the user ID parameter in the request. The application used this parameter to determine whose account information should be displayed.

6. Changed the user ID value from the logged-in user’s ID to the target user’s identifier, `carlos`.

7. Sent the modified request.

8. The server responded with:

   ```http
   HTTP/2 200 OK
   ```

9. Inspected the response body and found Carlos’s API key:

   ```text
   Your API Key is: BjRZo7swATsJDpGdLNUTenc4KxojMexI
   ```

10. Submitted the API key in the lab solution field.

11. The lab was successfully solved.

## Result

The application returned another user’s private account data after only changing the user ID parameter in the request. No additional authorization, authentication token, or server-side ownership check was required.

## Vulnerability Identified

The lab contained a **horizontal privilege escalation** vulnerability, also known as an **IDOR** (Insecure Direct Object Reference).

The application trusted a user-supplied identifier in the request without verifying that the authenticated user was authorized to access that identifier’s account. As a result, user A could access user B’s account information by changing the ID value.

Example request concept:

```http
GET /my-account?id=<victim-user-id> HTTP/2
Cookie: session=<authenticated-user-session>
```

The server should have checked whether the session user matched the requested user ID before returning sensitive data.

## Impact

This type of vulnerability can allow one authenticated user to access another user’s private data, such as:

- API keys
- Email addresses
- Personal details
- Order history
- Account settings
- Internal identifiers

In a real application, exposed API keys could lead to account takeover, unauthorized API access, data theft, or further privilege escalation.

## Recommended Remediation

- Use server-side authorization checks for every request involving user-specific data.
- Verify that the authenticated session user owns the requested resource before returning it.
- Avoid relying on client-side controls or obscured user IDs for security.
- Use unpredictable, non-sequential identifiers where appropriate, but never treat them as the only access-control mechanism.
- Return generic errors instead of revealing whether another user’s account exists.
- Log repeated access attempts to unfamiliar user IDs for monitoring and detection.
