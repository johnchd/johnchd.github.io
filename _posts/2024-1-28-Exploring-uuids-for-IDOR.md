IDORs are one of my favorite vulns. They're simple and lethal. 

An example of an IDOR would look like this:

You authenticate and see the following URL when accessing your profile:
```
https://test4jc.com/profile?id=112
```

If the application lacks proper authorization checks, you can modify the ID from 112 to 103, gaining unauthorized access to information associated with ID 103.


Unfortunately IDORs are not always this straightforward. Savvy developers implement the use a [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Now consider the following scenario on the same application but instead of an ID, it's using a UUID:

You authenticate and see the following URL when accessing your profile:
```
https://test4jc.com/profile?id=157de2c8-f0ca-4504-bbdc-4c1728663268
```

To mitigate against IDOR, the developer has implemented a UUID - making the possibility of brute-forcing near impossible.

# Vulnerable Scenario:

Imagine 2 users within an application, Admin and Basic:
```
Admin: e37c9138-d530-4d2e-b6ed-014f0ca88cc8
Basic: 157de2c8-f0ca-4504-bbdc-4c1728663268
```

Looking at the GUIDs we can see:
- 32-digit alphanumerical unique strings
- No shared/similar patterns
- Not able to be brute forced

If we swap the ID's we can see there is a lack of user-specific access controls resulting in IDOR. Some programs may accept an IDOR like this, but I personally wouldn't submit it unless there was a way to retrieve at least one other UUID.


# My methodology for IDOR's:

When testing for IDOR's, my methodology is broken down into three parts.

**Part 1 - Application Layer:**
- Does the application have any functionality that incorporates other users? For example:
	- Sending a message to other users
	- Sharing information (documents, files, resources, etc.)
	- Is there any posting of comments within the application?
		- Can you tag users, see likes, view comments of other users, etc.
	- Is there a subscription/follow feature where one can follow other users?

These are a few examples of ways UUID's can be leaked within the application. Dig deep and really understand what the application does, you may even discover a larger attack surface.

**Part 2 - Discovery:**
- Are there any other UUID's that have populated within the HTTP history of burp? Consider using the bcheck for [UUID's](https://github.com/PortSwigger/BChecks/blob/main/other/uuid-detected-guid-versions.bcheck)
- Is there any JS files? Parse them for any other API endpoints with Burp Extensions like [JS Miner](https://portswigger.net/bappstore/0ab7a94d8e11449daaf0fb387431225b)
- Is there an API? 
	- Fuzz recursively for files/directories 

**Part 3 - OSINT:**
- Is the UUID leaked within any GET requests? Try the [WayBack Machine](https://archive.org/web/) or [Google Dorking](https://www.imperva.com/learn/application-security/google-dorking-hacking/)

Context and intuition is important when finding IDORs. Think about how endpoints depend on eachother. Be creative. Go find some IDORs.

