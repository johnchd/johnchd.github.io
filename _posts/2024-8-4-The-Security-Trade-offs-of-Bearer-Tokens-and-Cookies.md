

Recently 0xTib3rius created a [post]([https://x.com/0xTib3rius/status/1817665317503455682](https://x.com/0xTib3rius/status/1817665317503455682)) on X about the "where to store JWTs".  I found it to be a good discussion topic as understanding session/local storage, httpOnly, SameSite attributes, CSPs, and CORs is essential to mitigating risks associated with XSS and CSRF attacks.

His post made me want to write about my thoughts on session vs local storage and unpack his tweet. This post will have 3 parts:
- Part 1 - Session Storage vs Local Storage
- Part 2 - Break Down of the post
- Part 3 - The Million Dollar Question

# Part 1 - Session Storage vs Local Storage:

Starting off, session and local storage are methods for storing data on the client-side in a web-app **and each has different characteristics and use cases.**

Session Storage:
- **Persistence**: Data is cleared when the tab or browser is closed (or until the user clears it)
- **Scope**: Accessible only to the domain that stored it

Local Storage
- **Persistence**: Data is NOT cleared when the browser is closed (only until the user deletes the information)
- **Scope**: Accessible only to the domain that stored it

The main difference between these types of storage comes down to the persistence of data, but keep in mind that **both are designed to be accessed via JavaScript**. Now, you may be wondering what kind of web app would use session storage versus local storage. Here are some examples:

**Session Storage - Multi-Step Form:**

Imagine you find a new website and begin shopping online. You find 3 items and add each one to your cart. After adding the items, you begin the checkout process, but you accidentally close your browser tab. When you return to the website, you see that your cart is now empty. This is because the cart data was stored in session storage, which is cleared when you close the tab or browser.

**Local Storage - User Preferences:**

You sign into an app online and change the theme from light to dark. You close your browser, and when you sign into the application 3 days later, your app is still in dark mode. This is because local storage persists even after the browser is closed and reopened, retaining your user preferences.

# Part 2 - Break Down of the post:


"_A lot of people will argue that if your app is vulnerable to XSS, cookies with HttpOnly are at least protected from JavaScript, while Session/Local Storage are designed to be accessed by JavaScript.

This might seem like cookies have a big advantage, but what this really means is that cookies cannot be stolen. However, since the browser automatically adds cookies to requests, any XSS attack could send valid requests and steal the data from the responses. So yes, while you don't get the actual session token, you can still use it, which is ultimately what an attacker wants to do anyway._"


While cookies with HttpOnly can't be accessed via JavaScript, many people argue that cookies are more secure than session/local storage. Initially, I thought this too, but although cookies can't be stolen via XSS due to the HttpOnly flag, there are still security concerns.

If the app is vulnerable to XSS, the attacker can use the XSS to make requests as if they were the authenticated user. **This is because browsers automatically include cookies in every request to the server.**

So despite the cookie having the HttpOnly flag set, the attacker can still impersonate the user and retrieve sensitive data (as if they had the actual session token).


"_Moreover, if you rely on cookies, your app needs to protect itself against CSRF. This has become easier with SameSite cookie attributes, but they are not without their limitations ([https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions)).

If you use Session/Local Storage and set session tokens via a non-Cookie header (e.g. Authorization), CSRF is impossible. It's important to note that if your app is vulnerable to XSS, all your CSRF protections will fail regardless of how you store session tokens._"


If your app relies on cookies, it needs to protect itself against CSRF. Why? **Because browsers automatically include cookies in every request.** This means that requests made from the user's browser to the server will include cookies, even authentication cookies.

Protections from CSRF Include:
- **SameSite Attribute**: Mitigates CSRF by restricting how cookies are sent with cross-origin requests.
- **Anti-CSRF Tokens**: Tokens included in forms that are then verified on the server to ensure the request originated from your site.
- **Referrer Validation**: Ensures the request's origin is from a trusted domain.

Now what if your app relies on authentication via a non-cookie header, such as an Authorization Bearer within session/local storage? In this case, **CSRF is not possible because CSRF relies on cookies being automatically included in cross-site requests.** Only cookies are sent automatically by the browser—not session/local storage.

If you follow a defense-in-depth approach and find your app vulnerable to XSS, **unfortunately all your CSRF protections will fail.** This is because the attacker can use the XSS vulnerability to access and use anti-CSRF tokens, and then perform any action that the user is authorized to do. The storage of cookies or the authentication mechanisms in session/local storage become irrelevant as the attacker can access these tokens via JavaScript (as intended).

# Part 3 - The Million Dollar Question:

So is an Authorization Bearer more secure than cookies..? It really depends.

The advantage of an Authorization Bearer token is that its NOT included included in every request by the browser, meaning that CSRF is almost completely prevented. While CSRF is prevented, the disadvantage is that IF an XSS is triggered the Authorization Bearer can be stolen as it is meant to be accessible via JavaScript - giving the attacker full control.

The advantage of cookies is that they can be set with the HttpOnly flag, preventing JavaScript (XSS) from accessing the cookie. While this may seem like an advantage, keep in mind that it opens the door to CSRF - as cookies are included in every request made by the browser.

To wrap-up:
- Authorization Bearers are more secure against CSRF, but require thought on mitigating XSS.
- Cookies are more secure with the HttpOnly flag, but require CSRF protection.
- If XSS is possible, CSRF can be bypassed

I'm not sure one method is more secure than the other. The security of each approach depends on the specific needs and threat model of your application. Implementing a defense-in-depth strategy will enhance the overall security of your application, regardless of the chosen method.
