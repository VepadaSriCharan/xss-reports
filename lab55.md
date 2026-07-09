# Reflected Cross-Site Scripting (XSS) in Equifax Help Center Search

## Summary

I identified a Reflected Cross-Site Scripting (XSS) vulnerability in a GET parameter of the application.

The application reflects user-supplied input inside a JavaScript context without properly escaping special characters. By breaking out of the existing JavaScript code, it is possible to inject arbitrary HTML and JavaScript that executes in the victim's browser.

An attacker can craft a malicious URL and trick a victim into opening it. Once visited, the injected JavaScript executes in the context of the vulnerable application.



## Vulnerability Type

**Reflected Cross-Site Scripting (Reflected XSS)**



## Vulnerable Parameter

```text
search
```



## Vulnerable URL

```text
https://kzlabs.in/55.php?search=MIkey%27">-</script><img+src=x+onerror=alert(1)>
```



## Steps to Reproduce

1. Open the vulnerable page.

2. Replace the value of the `search` parameter with the following payload:

   ```html
   "></script><img src=x onerror=alert(1)>
   ```

3. Visit the crafted URL:

   ```text
   https://kzlabs.in/55.php?search=MIkey%27">-</script><img+src=x+onerror=alert(1)>
   ```

4. Observe that the payload is reflected into the application's JavaScript context without proper output encoding.

5. The injected payload breaks out of the existing script and injects an `<img>` element with an `onerror` event handler.

6. The browser executes the JavaScript and displays an `alert(1)` popup.



## Payload Used

```html
"></script><img src=x onerror=alert(1)>
```



## Proof of Concept

The payload successfully breaks out of the existing JavaScript context and injects an `<img>` element containing an `onerror` event handler.

When the browser attempts to load the invalid image, the `onerror` event is triggered and executes the following JavaScript:

```javascript
alert(1)
```

This confirms that arbitrary JavaScript execution is possible.



<img width="1913" height="1025" alt="image" src="https://github.com/user-attachments/assets/d3107666-141b-4ae3-897f-c6f99496f293" />


## Impact

An attacker can craft a malicious link that, when opened by a victim, executes arbitrary JavaScript in the context of the vulnerable website. This could allow an attacker to manipulate page content, steal sensitive user information, or perform actions on behalf of an authenticated user.

## Recommendation

To mitigate this vulnerability:

- Use htmlspecialchars() (or an equivalent output-encoding function) before rendering user-controlled data in HTML.
- Filter or sanitize HTML tags to prevent malicious markup from being injected.
- Filter or restrict dangerous JavaScript methods such as alert(), confirm(), and prompt() where appropriate.
- Deploy a Web Application Firewall (WAF), such as Cloudflare WAF, to help detect and block malicious XSS payloads.
