# Cross-Site Scripting (XSS) via `javascript:` URI in Shopify Account Confirmation

## Summary

I identified a Cross-Site Scripting (XSS) vulnerability in the `returnTo` parameter of the application.

The application accepts a user-controlled URL through the `returnTo` parameter without validating the URI scheme. By supplying a `javascript:` URI instead of a legitimate destination, arbitrary JavaScript is executed when the victim clicks the **Continue** button.

An attacker can craft a malicious URL and trick a victim into interacting with the page. Once the victim clicks the button, the injected JavaScript executes in the context of the vulnerable application.

## Vulnerability Type

**Cross-Site Scripting (XSS)**

## Vulnerable Parameter

```text
returnTo
```

## Vulnerable URL

```text
https://kzlabs.in/57.php?returnTo=javascript:alert(1)
```

## Steps to Reproduce

1. Open the vulnerable page.

2. Replace the value of the `returnTo` parameter with the following payload:

   ```text
   javascript:alert(1)
   ```

3. Visit the crafted URL:

   ```text
   https://kzlabs.in/57.php?returnTo=javascript:alert(1)
   ```

4. Fill in the required fields if necessary.

5. Click the **Continue** button.

6. Observe that the browser executes the supplied `javascript:` URI and displays an `alert(1)` popup.

## Payload Used

```text
javascript:alert(1)
```

## Proof of Concept

The application accepts the value of the `returnTo` parameter as a navigation target without validating the URI scheme.

When the **Continue** button is clicked, the browser follows the supplied `javascript:` URI and executes the following JavaScript:

```javascript
alert(1)
```

This confirms that arbitrary JavaScript execution is possible.

<img width="1899" height="1030" alt="image" src="https://github.com/user-attachments/assets/7e710fc2-3205-4fa6-b775-3e74a0020564" />

<img width="1920" height="579" alt="image" src="https://github.com/user-attachments/assets/2618c444-f32d-4321-8041-d09e3ae75e58" />


## Impact

An attacker can craft a malicious link that, when opened by a victim, executes arbitrary JavaScript in the context of the vulnerable website. This could allow an attacker to manipulate page content, steal sensitive user information, or perform actions on behalf of an authenticated user.

## Recommendation

To mitigate this vulnerability:

- Use `htmlspecialchars()` (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Filter or sanitize HTML tags to prevent malicious markup from being injected.
- Filter or restrict dangerous JavaScript methods such as `alert()`, `confirm()`, and `prompt()` where appropriate.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
