# Stored Cross-Site Scripting (XSS) in AdPulse Network Reports

## Summary

I identified a Stored Cross-Site Scripting (XSS) vulnerability in the **Report Name** field of the **New Network Report** functionality.

The application stores user-supplied input without properly sanitizing or encoding it before displaying it to other users. By submitting a malicious payload in the **Report Name** field, arbitrary JavaScript is stored in the application's database and executed whenever the stored report is viewed.

An attacker can exploit this vulnerability by creating a malicious report. When another user, including an administrator, views the Network Reports page, the injected JavaScript executes in the context of their session.

## Vulnerability Type

**Stored Cross-Site Scripting (Stored XSS)**

## Vulnerable Parameter

```text
Report Name
```

## Steps to Reproduce

1. Log in to the application and navigate to the **Network Reports** page.

2. Click **New Network Report**.

3. In the **Report Name** field, enter the following payload:

   ```html
   "><img src=x onerror=alert(1)>
   ```

4. Fill in the remaining required fields and click **Run & Save Report**.

5. Return to the **Network Reports** page.

6. Observe that the payload is stored and rendered without proper output encoding.

7. Whenever another authenticated user, including an administrator, visits the **Network Reports** page, the stored payload executes and displays an `alert(1)` popup.

## Payload Used

```html
"><img src=x onerror=alert(1)>
```

## Proof of Concept

The supplied payload is stored in the application's database as the report name.

When the stored report is displayed on the **Network Reports** page, the browser renders the injected HTML and executes the `onerror` event of the injected `<img>` element.

```javascript
alert(1)
```

This confirms that arbitrary JavaScript is permanently stored and executed whenever the vulnerable page is viewed.

<img width="867" height="756" alt="image" src="https://github.com/user-attachments/assets/3424744e-b8f0-44aa-a50d-5e4acd8cf293" />

<img width="1566" height="464" alt="image" src="https://github.com/user-attachments/assets/abd4a3b9-4b0f-4c2e-8274-e2dc79a5f596" />

## Impact

An attacker can store a malicious payload that executes whenever another user views the affected page. If an administrator views the stored report, the injected JavaScript executes in the administrator's browser. This could allow an attacker to manipulate page content, steal sensitive information accessible to the administrator's session (such as session cookies where applicable), or perform actions on behalf of the administrator.

## Recommendation

To mitigate this vulnerability:

- Use `htmlspecialchars()` (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Filter or sanitize HTML tags to prevent malicious markup from being injected.
- Filter or restrict dangerous JavaScript methods such as `alert()`, `confirm()`, and `prompt()` where appropriate.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
