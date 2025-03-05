[Go back to book index](index)

# Notes on Bug Bounty Bootcamp Book




# Reconnaissance

1. **Navigate the application** as a normal user to discover all of the features users have access to.
2. Do **Google Dorking**, which consists of making use of a search engine to find valuable hidden information such as admin portals, unlocked password files and leaked authentication keys. By using Google queries, we are able to investigate further on a topic or application (For more see *Google search operators* and the [Google Hacking Database](https://www.exploit-db.com/google-hacking-database))
3. Begin the **scope discovery** stage, but make sure you do this only on allowed domains. Make sure to have the answer to the following questions: Which domains, subdomains and IP addresses can you attack? What company assets is the organization hosting on these machines?
4. **Get info about a target** using the `whois` command. Note that some organizations use a service called *domain privacy*. For these types of services you should conduct a [reverse whois](https://viewdns.info/reversewhois) search
5. **Locate the target's IP addresses** by using the command `nslookup`. Once you found the domain IP address perform a [reverse IP lookup](https://viewdns.info/reverseip/) to get all the domains registered with this IP address. Also run whois with that IP address to check the NetRange field. Using the command `whois -h whois.cymru.com <IP-address>` we can get the IP-to-ASN translation, which (if done with various IP addresses) we can figure out the range of IPs that belongs to the target.
6. By making a **Certificate Parsing** from online databases like [crt.sh](crt.sh), we can get a list of domains belonging to the target.
7. After discovering most of the target's domains, **discover its subdomains** using tools like Sublist3r, SubBrute, Amass and Gobuster. To use this tools you will need a wordlist (like [Daniel Miessler's SecLists](https://github.com/danielmiessler/SecLists), or generate your own using [Commonspeak2](https://github.com/assetnote/commonspeak2)). Then what you can do is combine the different wordlists you've found (make sure to delete duplicates). 
8. Once you've discovered a good number of subdomains, discover more by identifying patterns. Also look for subdomains of subdomains. You can find subdomains of subdomains by running enumeration tools recursively.
9. Using port scanning **look for available services** on the target. Port scanning has two variants, you can do either active or passive port scanning. The active port scanning is done with a tool like `nmap` and it directly connects to the server. A passive port scan can be done with third-party resources like *Shodan*, a search engine that lets the user find machines connected to the internet.
10. Discover the more attack surface by **brute forcing the directories** of the target's web servers. Through this directories, you can discover hidden admin panels, configuration files, password files, outdated functionalities, database copies and source code files. You can use **Dirsearch or Gobuster** for directory brute-forcing. Once you've found a bunch of directories, use a screenshot tool like *EyeWitness* instead of looking at each one manually.
11. Another way of discovering directories is by using **web spiders**, like the ZAP's spider.
12. Check whether the company depends on some **third party service**. For example, take a look at whether your target uses AWS's S3. This can be done by google dorking, doing some github recon or using other tools (like [GrayhatWarfare](https://buckets.grayhatwarfare.com/) and [Lazys3](https://github.com/nahamsec/lazys3) and [Bucket Stream](https://github.com/eth0izzle/bucket-stream)). After you've found a couple of buckets that belong to the target, use `awscli` to get access to it. Gather any useful information leaked via the bucket and use it for future exploitation. The target may have some API keys or personal information in there, which should be reported right away. Also, test whether you have write access to the bucket by uploading and deleting a file. Don't risk deleting or modifying your target's files.
13. **Search the organization on github** for sensitive data that has been committed. Look for its employees and their repositories. Then dive into the code looking for secret keys, vulnerable code or hidden API endpoints that can be useful in the exploitation stage.

# Cross Site Scripting (XSS)

## Stored XSS

This type of XSS happens when the attacker's script is injected to a database on the target's platform, and it displays on other users. 

This type of XSS is the most dangerous of all.

## Blind XSS

Is a stored XSS but instead of displaying it to the users, the injected content is executed in another part of the application or in another application that the attacker cannot see.

This type of XSS is often used to attack administrators, exfiltrate their data and compromise their accounts.

## Reflected XSS

The scripts injected are only seen by the attacker, without being stored on a database. 

An attacker can trick victims into displaying a website with injected scripts.

## DOM-Based XSS

It is similar to Reflected XSS, but there aren't any HTTP requests to the server involved. The injected scripts are only executed by the client's browser.

Attackers are able to attack the DOM when a page takes user-supplied data and dynamically alters the DOM based on that input.

## Self-XSS 

These XSS require the client to input a malicious script on an input that only them can see. The attacker has no way of modifying this types of input without interacting with the victim.

In bug bounties, these types of XSS are not accepted as valid because they require social engineering.

## Hunting for XSS

1. Look for input opportunities


## Useful links

https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

Search XSS Payloads

EdOverflow XSS polyglot

https://github.com/mandatoryprogrammer/xsshunter

https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html
