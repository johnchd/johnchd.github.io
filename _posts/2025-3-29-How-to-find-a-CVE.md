If you do anything type of work in security you will have heard of the term 0-day or CVE. A CVE is simply "[..a list of publicly disclosed computer security flaws.](https://www.redhat.com/en/topics/security/what-is-cve)" This means a CVE can be any security flaw in any piece of software. How hard can it be to find a vulnerability in ONE of the millions and millions of free and open source software.

Still the challenge is finding the right project to test on. I read some articles and watched some videos. The best one I saw was from [Joe Helle](https://www.youtube.com/watch?v=epBR-9jF4r8). His methodology led me to finding OpenMRS.

```
medical records foss
```

<img width="944" alt="image" src="https://github.com/user-attachments/assets/b75ebcc0-6e1d-4457-a9da-2545f01a0588" />

This methodology worked but there were some problems. I'd find a project, spin it up on my VM, fail, download the required dependencies, and then finally get the project running just to find out that the app is bullet-proof... But this can all be avoided with Github Filters.

Use this as a template:

```
language:PHP Management System in:readme,description pushed:>2024-01-01 "docker-compose.yml" OR "Dockerfile" demo
```

Lets break it down:
- Language: PHP
- Management System in:readme,description
- pushed:>2024-01-01
- "docker-compose.yml" OR "Dockerfile"
- demo

With the query above, were specifying the language and management system. Management systems have a lot of logic that can be misconfigured (tenants, users, creating, reading, updating, deleting, etc). Lastly were specifying that the repo must be somewhat active (contributed to since 2024), have a docker file, and a demo website.

One of my future projects is to find a CVE for every vulnerability class and I'll be using a variation of the Github filter above. More to come on that later....

# CVE Submission

Say you find a bug and want to submit a CVE. The process can be confusing if you've never done it before. I'll break it down:

1. Notify the vendor.
2. After confirmation of the vuln(s), submit them to [MITRE](https://cveform.mitre.org/).
3. Wait for MITRE to give you a CVE ID.
4. Receive CVE ID from MITRE.
5. Public disclose your vulnerability (you can see my public disclosure [here](https://github.com/johnchd/CVEs/tree/main/OpenMRS)).
6. Notify MITRE of your public disclosure.
7. Wait for response back from MITRE telling you your CVE is now public.

For more information, read these links:
- https://cve.mitre.org/docs/docs-2016/CVE_Request_Web_Form_Tip_Sheet.pdf 
- https://cve.mitre.org/CVEIDsAndHowToGetThem.pdf 

I followed the processes above and got the following 4 CVE's accepted:

- [CVE-2025-25925](https://nvd.nist.gov/vuln/detail/CVE-2025-25925)
- [CVE-2025-25927](https://nvd.nist.gov/vuln/detail/CVE-2025-25927)
- [CVE-2025-25928](https://nvd.nist.gov/vuln/detail/CVE-2025-25928)
- [CVE-2025-25929](https://nvd.nist.gov/vuln/detail/CVE-2025-25929)

Timeline:
- 1/22/25 - Submitted 4 CVEs to MITRE
- 2/26/25 - MITRE assigned me 4 CVE IDs
- 2/26/25 - Publicly disclose 4 CVEs Github and notify MITRE
- 3/11/25 - MITRE published my CVEs


