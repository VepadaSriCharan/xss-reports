# Reflected Cross-Site Scripting (XSS) in Imgur Messages Profile

## Summary

I identified a Reflected Cross-Site Scripting (XSS) vulnerability in the user-controlled username portion of the application's URL.

The application reflects the username directly into the HTML response without properly escaping special characters. By injecting a specially crafted payload into the username, it is possible to inject arbitrary JavaScript that executes in the victim's browser.

An attacker can craft a malicious URL and trick a victim into opening it. Once visited, the injected JavaScript executes in the context of the vulnerable application.



## Vulnerability Type

**Reflected Cross-Site Scripting (Reflected XSS)**


## Vulnerable Input

```text
User-controlled username in the URL path
```



## Vulnerable URL

```text
https://kzlabs.in/58.php/account/Mikey1"><script>alert(1)</script>/messages
```



## Steps to Reproduce

1. Open the Messages page and click on any username.

2. Observe that the username appears in the URL path.

3. Replace the username with the following payload:

   ```html
   Mikey1"><script>alert(1)</script>
   ```

4. Visit the crafted URL:

   ```text
   https://kzlabs.in/58.php/account/Mikey1"><script>alert(1)</script>/messages
   ```

5. Observe that the supplied username is reflected in the page without proper output encoding.

6. The injected `<script>` element executes and displays an `alert(1)` popup.



## Payload Used

```html
"><script>alert(1)</script>
```



## Proof of Concept

The payload successfully breaks out of the HTML context and injects a new `<script>` element.

The browser executes the injected JavaScript and displays the following popup:

```javascript
alert(1)
```

This confirms that arbitrary JavaScript execution is possible.



<img width="1877" height="846" alt="image" src="https://github.com/user-attachments/assets/d6d98623-05e5-4a4e-a00c-873972fd6402" />


<img width="1920" height="466" alt="image" src="https://github.com/user-attachments/assets/3ef56e5b-5eaf-4a07-8b5a-dbef170e9b2d" />

## Impact

An attacker can craft a malicious link that, when opened by a victim, executes arbitrary JavaScript in the context of the vulnerable website. This could allow an attacker to manipulate page content, steal sensitive user information, or perform actions on behalf of an authenticated user.

## Recommendation

To mitigate this vulnerability:

- Use htmlspecialchars() (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Filter or sanitize HTML tags to prevent malicious markup from being injected.
- Filter or restrict dangerous JavaScript methods such as alert(), confirm(), and prompt() where appropriate.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
