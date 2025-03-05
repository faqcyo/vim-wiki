[Back to Notes Index](index)

# Lessons on CTF

When working on SQLite, we can retrieve the whole database schema using sqlite_master (in case of the sqlite3), with the following query:

```
SELECT sql FROM sqlite_master
```

For each functionality, think about what an attacker would do to compromise them. For example, in an ecommerce site, an attacker may want to get credit card numbers.

Also rank each vulnerability that you find, and prioritize exploiting the one that potentially is more harmful.

For BB Reports:

1. Title
2. Severity
3. Description
4. Reproduction Steps
5. Impact
6. Mitigation
7. Affected assets (Generally a list of affected URLs)

For the severity part, these are some available options:

- Informational: Issue has no real impact
- Low: The business impact is minimal
- Medium: Potential to cause harm to users, but not revealing any data
- High: Potential to reveal user data or aids in exploitation of other vulnerabilities
- Critical: High risk of personal/confidential data exposure, general system compromise, and other severe impacts to the business.


When trying XSS beware of the Web Browser Firewall and its HTML parser

Content Sniffing, MIME Sniffing (IE 6/7 bugs). Encoding Sniffing. To prevent all of these sniffing, the defender should specify the encoding and MIME type.

SOP <=> Same Origin Policy... Protocol, ports used and domain names must match between requests.
CORS <=> Cross-Origin Resource Sharing... It's new, so research more on this

Pretty much no website that receives messages, validate them properly.

CSRF (Cross-Site Request Forgery) Attacks

Forced Browsing / Direct Object Reference ... Improper Authorization / Auth-z

**IMPORTANT: Don't abuse exploits on target websites, use POC (Proof of Concept) when reporting it**

**Session Fixation** is an exploitable vulnerability that appears primarily on older versions of PHP (with the PHPSESSID). Basically, every user has its own identifier from which its session is identified. That is, if a user logs in, we assign a session id, then if it logs in again we assign the same session id. This vulnerability can be completely mitigated by generating a new session id every time a user logs to the website, and deleting older session ids. 
