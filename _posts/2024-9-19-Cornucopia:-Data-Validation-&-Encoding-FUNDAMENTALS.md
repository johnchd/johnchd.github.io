Finding vulnerabilities is one thing but speaking with a development team on how to fix the vulnerabilities is equally as important (if not more) than finding the vulnerability in my opinion.

While going through Data Validation and Encoding suit from OWASP Cornucopia, I figured I'd write a quick blog on Validation, Sanitization, Encoding, and Escaping. 

# Example

Imagine a web app that allows user to post comments on a blog.

A user submits:
```
<script>alert(document.domain)</script>
```

If the app does not sanitize or encode the input, the browser will interpret that comment as JavaScript and execute it in the browser.

# Validation

Validation ensures user input meets the expected format, type, and constraints before it is stored. 

Consider an application expects a user to enter there age. If validation is implemented, the application will expect:
*a positive number
*a range between 1-100

So if a user tried to enter:
```
<script>alert(document.domain)</script>
```

It would never be stored, because it doesn't meet the expected criteria of a persons age.

Types of validation include:
*Client-Side: performed in the users browser using JS (easily bypassed)
*Server-Side: performed on the server where it can not be tampered with
*Field-Specific: 
		- Length
		- Type (valid phone number, email address, zip code)
		- Format (using regular expressions)
		- Range (age 18-24)

Validation does not only prevent injection attacks, but also ensures data integrity.


# Sanitization

Sanitization removes the dangerous parts of the input.

Consider the following example:
```
<script>alert(document.domain)</script>
```

Sanitized input will remove the script tag, resulting in:
```
alert(document.domain)
```

In some cases it may just remove the special characters, or it may only allow tags that are specified on a whitelist.

Sanitizing is a great way to prevent XSS and in some cases a better way to break user functionality... For example, if users need to enter HTML for a blog post (like headers, hyperlinks, etc), stripping all tags will make this part of the app unusable... This is where encoding comes into play.


# Encoding 

Encoding converts special characters to a format that the browser will NOT interpret as code.

Consider the same example:
```
<script>alert(document.domain)</script>
```

```
< becomes &lt;
> becomes &gt;

so the example becomes:
&lt;script&gt;alert(document.domain);&lt;/script&gt;
```

Now the browser treats it as plain text and NOT as code. Examples of when an application may want to render user input is:
	- An app may allow users to create/format a blog post, comments, or messages
	- An app may have user input inserted into HTML fields, and then placed into a PDF

Note that the above example is HTML encoding, and each data context (HTML, JavaScript, URL, etc.) requires different types of encoding.

# Escaping

Escaping prevents user input from being executed as code. It's most relevant SQL.

For example, if a user input the following **username** in a login field:
```
admin' or 1=1 --
```

The SQL query becomes:
```sql
SELECT * FROM users WHERE username = 'admin' or 1=1 --';
```

allowing the user to log in as admin without needing the password.

With escaping, the single quote is treated as PART OF THE USERNAME and not as SQL:
```sql
SELECT * FROM users WHERE username = 'admin\' OR 1=1 --';
```
