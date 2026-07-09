# Stored Cross-Site Scripting (XSS) in Quill Article Body

## Summary

I identified a Stored Cross-Site Scripting (XSS) vulnerability in the **Body** field of the article editor when using the **HTML** mode.

The application stores user-supplied HTML without properly sanitizing or encoding it before rendering it on the **My Articles** page. As a result, an attacker can inject arbitrary JavaScript that is stored in the application's database and executed whenever the article is viewed.

An attacker can exploit this vulnerability by publishing a malicious article. When another user views the article, the injected JavaScript executes in the context of their session.

## Vulnerability Type

**Stored Cross-Site Scripting (Stored XSS)**

## Vulnerable Field

```text
Body
```

## Steps to Reproduce

1. Log in to the application and navigate to the **Write Article** page.

2. Switch from **Write** mode to **HTML** mode.

3. Enter the following payload into the **Body** field:

   ```html
   "><ScRiPt>alert(1)</ScRiPt>
   ```

4. Provide a title and publish the article.

5. Navigate to the **My Articles** page.

6. Observe that the payload is stored and rendered without proper output encoding.

7. The injected JavaScript executes automatically and displays an `alert(1)` popup whenever the article is viewed.

## Payload Used

```html
"><ScRiPt>alert(1)</ScRiPt>
```

## Proof of Concept

The supplied payload is stored in the application's database as part of the article body.

When the article is rendered on the **My Articles** page, the stored HTML is inserted directly into the page without sanitization, causing the browser to execute:

```javascript
alert(1)
```

This confirms that arbitrary JavaScript is permanently stored and executed whenever the article is viewed.

<img width="1022" height="868" alt="image" src="https://github.com/user-attachments/assets/0285fdcc-ced3-476e-a4df-c1a05339a81c" />


<img width="1745" height="547" alt="image" src="https://github.com/user-attachments/assets/8a71f255-ce31-4958-a2f9-ae768c18496b" />


## Impact

An attacker can store a malicious payload that executes whenever another user visits the affected page. If an administrator or another privileged user views the stored content, the injected JavaScript executes in their browser. This could allow an attacker to manipulate page content, steal sensitive information, or perform actions on behalf of the affected user.

## Recommendation

To mitigate this vulnerability:

- Use `htmlspecialchars()` (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Validate and sanitize user input before storing it in the database.
- Apply context-aware output encoding whenever stored user input is rendered.
- Deploy a strong Content Security Policy (CSP) to reduce the impact of XSS vulnerabilities.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
