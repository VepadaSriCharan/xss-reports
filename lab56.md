# Reflected Cross-Site Scripting (XSS) in PUBG Community Feed Search

## Summary

I identified a Reflected Cross-Site Scripting (XSS) vulnerability in a GET parameter of the application.

The application reflects user-supplied input inside an HTML attribute without properly escaping special characters. By breaking out of the attribute context, it is possible to inject arbitrary JavaScript that executes in the victim's browser.

An attacker can craft a malicious URL and trick a victim into opening it. Once visited, the injected JavaScript executes in the context of the vulnerable application.

---

## Vulnerability Type

**Reflected Cross-Site Scripting (Reflected XSS)**

**Context:** HTML Attribute

---

## Method

```text
GET
```

---

## Vulnerable Parameter

```text
p
```

---

## Vulnerable URL

```text
https://kzlabs.in/56.php?p=MIkey%27><ScRipT>confirm(1)</ScRipT>
```

---

## Steps to Reproduce

1. Open the vulnerable page.

2. Replace the value of the `p` parameter with the following payload:

   ```html
   "><ScRipT>confirm(1)</ScRipT>
   ```

3. Visit the crafted URL:

   ```text
   https://kzlabs.in/56.php?p=MIkey%27><ScRipT>confirm(1)</ScRipT>
   ```

4. Observe that the payload is reflected inside the `data-query` HTML attribute without proper output encoding.

5. The injected payload breaks out of the HTML attribute, creates a new `<script>` element, and executes JavaScript.

6. A `confirm(1)` dialog box appears, confirming successful JavaScript execution.

---

## Payload Used

```html
"><ScRipT>confirm(1)</ScRipT>
```

---

## Proof of Concept

The payload successfully breaks out of the `data-query` HTML attribute and injects a new `<script>` element.

The browser executes the injected JavaScript and displays the following popup:

```javascript
confirm(1)
```

This confirms that arbitrary JavaScript execution is possible.

---

<img width="1918" height="1013" alt="image" src="https://github.com/user-attachments/assets/fdcca118-c028-4d84-b6f0-63c33b15c732" />


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

- Properly encode user input before placing it inside HTML attributes.
- Escape special characters such as `"`, `'`, `<`, `>`, and `&`.
- Perform strict input validation where appropriate.
- Implement contextual output encoding based on where the data is rendered.
- Deploy a strong Content Security Policy (CSP) to reduce the impact of XSS vulnerabilities.

---
