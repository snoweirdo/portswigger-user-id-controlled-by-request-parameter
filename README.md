## This lab demonstrates a horizontal privilege escalation vulnerability caused by an insecure direct object reference (IDOR).

1. Logged in as `wiener`.
2. Opened the **My account** page and captured the request in Burp.
3. Sent the request to Repeater.
4. Changed the `id` parameter from the logged-in user’s ID to `carlos`.
5. Sent the modified request and received `HTTP/2 200 OK`.
6. Found Carlos’s API key in the response.
7. Submitted the API key to solve the lab.

The application failed to verify that the authenticated user was authorized to access the requested account, allowing one user to retrieve another user’s sensitive data.
