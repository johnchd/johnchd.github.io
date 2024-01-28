IDORs are one of my favorite vulns - simple, easy, and yet so lethal. If you're reading this post, you are likely familiar with what an IDOR is, but in the event you're not, keep reading.

IDOR stands for Indirect Object Reference and is used to to create/read/update/delete (CRUD) on data by exploiting improper authorization. A simple example of an IDOR would look like this:

You authenticate and see the following URL when accessing your profile:
```
https://test4jc.com/profile?id=112
```

If the application lacks proper authorization checks, one can modify the ID from 112 to 103, granting *unauthorized* access to all the information associated with ID 103.


# Introduction to UUID's:

Unfortunately, IDORs are not always this straightforward (although sometimes they are). To mitigate, developers may use a [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Now consider the following scenario on the same application but instead of an ID, it's using a UUID:

You authenticate and see the following URL when accessing your profile:
```
https://test4jc.com/profile?id=3s7k-9fz8-a1y2-v0p6
```

To prevent IDOR, the developer has implemented a 16 character alphanumerical UUID. Despite it being nearly impossible to brute force this UUID, does this mean we throw the potential of the IDOR away? Absolutely not!!!!!!! 

# Vulnerable Scenario:

Imagine the following two users (Admin & Basic) within an application, and each user had the associated UUID:
```
Admin: a1b2-c3d4-e5f6-g7h8
Basic: x9y8-z7w6-v5u4-t3s2
```

Right off the bat, we can see:
- 16-digit alphanumerical unique strings
- No shared or similar patterns
- Not able to be brute forced

Despite the uniqueness, when we swap the UUID's we can see there is a lack of user-specific access controls, resulting in IDOR. Some programs *may* accept an IDOR like this, but ultimately it is [low](https://www.first.org/cvss/calculator/3.0#CVSS:3.0/AV:N/AC:H/PR:L/UI:N/S:U/C:N/I:L/A:N) on the CVSS scale as the 16-digit UUID is unable to be brute-forced. 

BUT, we know that the application is vulnerable to IDOR - and all we need is some ID's.

# My methodology for IDOR's:

When testing for IDOR's, my methodology is broken down into three parts.

**Part 1 - Application Layer:**
- Does the application have any functionality that incorporates *other* users? Examples include:
	- Sending a message to other users
	- Sharing information (documents, files, resources, etc)
	- Is there any posting of comments within the application?
		- If there is posting of comments, is there tagging of other users?
	- Is there a subscription/follow feature where one can follow other users?

These are a few basic examples of ways UUID's can be leaked within the application. Dig deep and strive to understand *what* the application actually does. By understanding what the application actually does, you'll find even more vulns (access control, info disclosure, etc). It's possible the application may not have any of these features, but theres still more ways UUID's can be disoovered.

**Part 2 - Content Discovery:**
- Are there any other UUID's that have populated within the HTTP history of burp? Consider using the bcheck for [UUID's](https://github.com/PortSwigger/BChecks/blob/main/other/uuid-detected-guid-versions.bcheck)
- Is there any JS files? Parse them for any other API ep's with burp extensions like [JS Miner](https://portswigger.net/bappstore/0ab7a94d8e11449daaf0fb387431225b)
- Is there any API calls? Get fuzzin!!!!! it cant hurt.
- If there is any API calls? 
	- Chop down the directories and see if you can access anything else. 
	- Is there any API calls specifying a version like /api/v3/update-profile? Change the /v3/ to /v2/.

**Part 3 - OSINT:**
- Is the UUID leaked within any GET requests? Perhaps you can retrieve the ID via the [WayBack Machine](https://archive.org/web/) or [Google Dorking](https://www.imperva.com/learn/application-security/google-dorking-hacking/)

If all these fail, there are still plenty of other strategies within the request itself. I've found a handful of IDORs using this strategy and wanted to write it out for myself and figured others might find some value in it.


