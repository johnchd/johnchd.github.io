
What is a cookie?

A cookie is simply a small piece of data that a web server uses for:
- Session Management: logins, shopping carts, scores, etc
- Personalization: user preferences (like language), themes, settings, etc
- Tracking: recording and analyzing behavior

But theres so much more!!!!

After the server receives the HTTP request, the server can send one or more `Set-Cookie` headers with the response.

# So what is the Set-Cookie header you may ask?

The `Set-Cookie` header is just a response header used to set or update cookies based on the specified attributes.

The Set-Cookie header typically contains the following attributes:
- Cookie name/value
- Expiration
- Domain
- **Secure** 
- **HttpOnly** 
- **Same-Site**

The following is an example of the `Set-Cookie` syntax:
```
Set-Cookie: sessionID=<value>; Path=/<Path>; Expires=Wed, 09 Jun 2023 10:18:14 GMT; Secure; HttpOnly; SameSite:<Strict|none|lax>
```

# Why is Cookie Security important?

Misconfigured cookies can lead to the breach of confidentiality and integrity as a result of cookie-related attacks such as XSS, CSRF and session-fixation. But how can this happen?

Cookie-related attacks can stem from:
- The reuse/persistence of cookies 
- Cookies being accessible via all subdomains 
- The transmission of cookies via HTTP 
- JavaScript accessing cookies 
- The transmission of cookies in cross-site requests 

**Now how can this be fixed? Consider the implementation of the following headers:**

***Expiration:***
There is no "set-it-and-forget-it" configuration for a cookie, as the configuration is specific to the use case of the application. Ideally, cookies should be configured with the shortest lifespan as possible in order to reduce the window of opportunity for an attacker. However, a short expiration will require frequent re-authentication which can impact user experience. 

Its important to note that if a site authenticates users, it should regenerate and resend cookies (even if cookies are not expired) every time a user authenticates. Addressing the reuse of cookies (and the uniqueness of a cookie) helps mitigate session fixation attacks.

***Domain:***
Extra thought should be put into setting the Domain attribute. This is because if the `Set-Cookie` header field **does not** have the `Domain` attribute (which is default), the effective domain is the domain of the request.

However, consider the following example where a developer wants to specify the cookie for only a certain domain:

The website is named test4jc.com, and the developer sets the domain attribute to test4jc.com as so:
```
Set-Cookie: myCookie=myValue; Domain=test4jc.com; Path=/; Expires=Wed, 01 Jan 2025 00:00:00 GMT; Secure; HttpOnly; SameSite=Strict
```

Explicitly setting the `Domain` attribute to `test4jc.com`, the cookie is *automatically* converted to `.test4jc.com`, and in turn will be available to `test.test4jc.com`. By using the domain attribute, the developer has inadvertently opened up this cookie for all subdomains of `test4jc.com`.

***Secure:***
The flag will prevent the cookie from being transmitted via HTTP - and the cookie will ONLY be transmitted via HTTPS. 

Setting the Secure flag solves the problem of transmission of cookies via HTTP, by automatically changing to HTTPS.

***HTTPOnly:***
This flag makes the cookie NOT accessible via JavaScript. This means the attacker will not be able to extract the cookie in the event XSS is possible.

Setting the HTTPOnly flag solves the problem of JavaScript being able to access cookies, mitigating the impact of XSS.

***SameSite:***
The SameSite flag helps control how cookies are sent along with cross-origin requests. The SameSite flag is responsible for preventing vulnerabilities such as CSRF and mitigating against XSS.

***SameSite:*** `Strict` - Cookies are only sent when:
- a user navigates directly to the site  
- a user enters the URL
- submitting a form on the site itself
While Strict is the most secure as cookies are not sent with cross-origin requests, this flag restricts website functionality.

***SameSite:*** `Lax` - Cookies are only sent when:
- A user enters the URL
- A user clicks a link from an external site
NOTE: Lax is also the default of SameSite Cookies if an attribute is not set.

**So whats the main difference between strict and lax?**
- Strict sends the cookie ONLY for same-site requests
- Lax however, one can click follow a link from an external website and the cookies will be sent

***SameSite:*** `None` - Cookies are always sent, BUT requires the ***Secure*** attribute.


# Putting it all together:

Cookies serve multiple purposes - from enabling session management, to personalization preferences, and even user tracking. In addition to the cookie header, the `Set-Cookie` header is also used with attributes such as expiration, domain, `Secure`, `HttpOnly`, and `SameSite`. Ensuring the `Set-Cookie` flags *(specifically Secure, HttpOnly, and SameSite)* are set properly is important to prevent cookie-related attacks such as XSS, CSRF, and session-fixation.





