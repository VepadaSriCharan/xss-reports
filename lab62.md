# Stored Cross-Site Scripting (XSS) in Acronis Forum Profile Signature

## Summary

I identified a Stored Cross-Site Scripting (XSS) vulnerability in the **Signature** field of the user profile.

The application stores user-supplied input from the **Signature** field without properly sanitizing or encoding it before rendering it. As a result, an attacker can inject arbitrary JavaScript that is permanently stored in the application's database and executed whenever the signature is displayed.

An attacker can exploit this vulnerability by saving a malicious signature. When another user or an administrator views a page displaying the attacker's signature, the injected JavaScript executes in the context of their session.

## Vulnerability Type

**Stored Cross-Site Scripting (Stored XSS)**

## Vulnerable Field

```text
Profile Signature
```

## Steps to Reproduce

1. Log in to the application and navigate to **My Profile**.

2. In the **Signature** field, enter the following payload:

   ```html
   "><img src=x onerror=alert(1)>
   ```

3. Save the profile.

4. Create a new forum post.

5. Open the post as another user and view the post.

6. Observe that the stored signature is rendered along with the post without proper output encoding.

7. The injected `<img>` element executes the `onerror` event and displays an `alert(1)` popup.

## Payload Used

```html
"><img src=x onerror=alert(1)>
```

## Proof of Concept

The supplied payload is stored in the application's database as the user's profile signature.

When the signature is rendered on the application, the browser interprets the injected HTML and executes the following JavaScript:

```javascript
alert(1)
```

This confirms that arbitrary JavaScript is permanently stored and executed whenever the signature is viewed.

<img width="1484" height="852" alt="image" src="https://github.com/user-attachments/assets/ef340473-2b8d-4410-90f3-b0e7f9f55ae6" />

## Impact

An attacker can store a malicious payload that executes whenever another user visits the affected page. If an administrator or another privileged user views the stored signature, the injected JavaScript executes in their browser. This could allow an attacker to manipulate page content, steal sensitive information, or perform actions on behalf of the affected user.

## Recommendation

To mitigate this vulnerability:

- Use `htmlspecialchars()` (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Validate and sanitize user input before storing it in the database.
- Apply context-aware output encoding whenever stored user input is rendered.
- Deploy a strong Content Security Policy (CSP) to reduce the impact of XSS vulnerabilities.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
