# Reflected Cross-Site Scripting (XSS) in Imgur Messages Profile

## Summary

I identified a Reflected Cross-Site Scripting (XSS) vulnerability in the user-controlled username portion of the application's URL.

The application reflects the username directly into the HTML response without properly escaping special characters. By injecting a specially crafted payload into the username, it is possible to inject arbitrary JavaScript that executes in the victim's browser.

An attacker can craft a malicious URL and trick a victim into opening it. Once visited, the injected JavaScript executes in the context of the vulnerable application.

---

## Vulnerability Type

**Reflected Cross-Site Scripting (Reflected XSS)**

**Context:** HTML Context

---

## Method

```text
GET
```

---

## Vulnerable Input

```text
User-controlled username in the URL path
```

---

## Vulnerable URL

```text
https://kzlabs.in/58.php/account/Mikey1"><script>alert(1)</script>/messages
```

---

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

---

## Payload Used

```html
"><script>alert(1)</script>
```

---

## Proof of Concept

The payload successfully breaks out of the HTML context and injects a new `<script>` element.

The browser executes the injected JavaScript and displays the following popup:

```javascript
alert(1)
```

This confirms that arbitrary JavaScript execution is possible.

---

<img width="1877" height="846" alt="image" src="https://github.com/user-attachments/assets/d6d98623-05e5-4a4e-a00c-873972fd6402" />


<img width="1920" height="466" alt="image" src="https://github.com/user-attachments/assets/3ef56e5b-5eaf-4a07-8b5a-dbef170e9b2d" />

## Impact

An attacker could exploit this vulnerability by sending a crafted link to a victim. If the victim opens the link, arbitrary JavaScript executes in the context of the vulnerable website.

Potential impacts include:

- Execution of arbitrary JavaScript.
- Modification of page content.
- Credential theft through phishing pages.
- Session hijacking (where applicable).
- Performing actions on behalf of authenticated users.
- Stealing sensitive information accessible to the current user.

---

## Recommendation

To mitigate this vulnerability:

- Properly validate and encode user-controlled values before rendering them in the HTML response.
- Escape special characters such as `"`, `'`, `<`, `>`, and `&`.
- Perform strict input validation on values extracted from the URL path.
- Apply contextual output encoding based on where the data is rendered.
- Deploy a strong Content Security Policy (CSP) to reduce the impact of XSS vulnerabilities.
