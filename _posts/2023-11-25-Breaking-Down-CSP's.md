To understand what a CSP is, its important to first understand what a website is generally made up of:
- **HTML:** provides the text for the web page
- **CSS:** stylizes and enhances the visual appeal of the web page
- **JavaScript:** makes the webpage interactive
- and then on top of all that there's images, videos, fonts, etc
  
So what is a CSP?
***A CSP is a mechanism used to enforce what content can be loaded and used by a website.***

The goal of a Content Security Policy is to block *all* inline JavaScript (which is JavaScript directly within the HTML of a webpage), unless the JavaScript comes from a trusted URL. Since the JavaScript only comes from trusted URLs, it helps mitigate against XSS.

# CSP Syntax, Directives, and Values

CSP's are defined by policy directives, which then consist of values.

The syntax of a CSP looks like this:
```
Content-Security-Policy: <directive1> <value1>; <directive2> <value2>; <directive3> <value3>;
```

Examples of **policy** directives include: `default-src`, `script-src`, `style-src`, etc.

Examples of **directive** values include: `'self'`, `'unsafe-inline'`, `'unsafe-eval'`, `'none'`, etc.

Commonly used directives include:
1. **default-src:** Sets the default policy for loading resources such as scripts, stylesheets, images, fonts, etc.
2. **script-src:** Controls which sources can execute JavaScript.
3. **style-src:** Specifies the allowed sources for stylesheets.
4. **img-src:** Defines the sources from which images can be loaded.
5. **font-src:** Specifies the sources for loading fonts.
6. **media-src:** Defines the sources for loading media files (audio or video).
7. **frame-src:** Specifies the sources that can be used in a `<frame>`, `<iframe>`, `<object>`, or `<embed>`.

Commonly used directive values include:
1. **None:** Specifies that no resources are allowed to be loaded or executed. It disables the resource specified  from being loaded.
2. **All:** Loads resources from any source, including both the same origin and external sources. It allows all for the resource specified. 
3. **Self:** Restricts resources to be loaded or executed from the same origin as the webpage. 
4. **Data:** Allows resources to be loaded from `data:` URIs. These URIs embed the resource data directly into the webpage, such as inline images or fonts encoded as Base64.


Note - there is also "unsafe" directives. These directives should be used with caution, as the name states they are unsafe:
1. **Unsafe Inline:** Permits the execution of inline JavaScript directly embedded within the HTML markup. 
	1. This is unsafe, because it allows the use of any inline (embedded) JavaScript within the HTML of the webpage to be executed.
2. **Unsafe Hashes:** Allows the execution of JavaScript based on their hashes. So this directive permits inline JavaScript if their hashes match the hashes specified in the policy. 
	1. Its unsafe because IF an attacker bypassed the security checks, then JS can be executed via XSS.
3. **Unsafe Eval:** Allows the use of the `eval()` function and similar methods to execute dynamically generated code. 
	1. This is unsafe because an attacker can abuse eval() sink to execute DOM-based vulns

# Examples of CSP configurations:

1.) Imagine a developer wanted to allow all content to come from only the website it self:
```
Content-Security-Policy: default-src 'self'
```

This configuration allows loading resources such as scripts, stylesheets, images, etc., ONLY from itself. Note: The above excludes all subdomains.

2.) Imagine a developer wants to allow content from a trusted domain along with all of its subdomains:
```
Content-Security-Policy: default-src 'self' example.com *.example.com
```

This configuration allows loading resources such as scripts, stylesheets, images, etc., from the specified domains *including* itself, example.com, and subdomains of example.com
