# Borormir

**DISCLAIMER**

This report is intended only for the use of the individual or entity to which it is addressed and may contain information that is privileged, confidential, and exempt from disclosure under applicable law. If the reader of this disclaimer is not the indented recipient, you are hereby notified that any dissemination, distribution, or copying of this document is strictly prohibited. If you receive this document in error, please notify the creator immediately by telephone and return the original document to the owner.

## Executive Summary

This report documents the findings of a penetration test carried out by Noah Beckman against the targets fw-rivendell and Boromir. The project was completed in September 2022 and revealed the existence of four security-relevant issues. The completion of the assessment took a total of three days.&#x20;

The main target in scope was a computer running a WordPress Website and a WebSVN internal site. Methodology wise the assessment followed a black-box approach, meaning there was no access to the source code or file system. The Cyber.local team provided the environment and reset capabilities.

The aforementioned compromises involve one actual security vulnerabilities (CVE) and multiple general weaknesses and misconfigurations. The majority of these discoveries received a high severity ranking as root level compromise was possible as well as RCE potentially disclosing PII information.&#x20;

### Some Definitions

**Hacker**: term given by the public that the cyber security industry more accurately call and attacker or intruder.

**Vulnerability:** Typically a bug or a misconfiguration in a computer program, or computer that can be abused to gain access on a computer

**Exploit:** A program or strategy to exploit a vulnerability.&#x20;

**Privilege Escalation:** A technique to escalate privileges on a computer, often to an admin user.

**Metasploit/Meterpreter:** A tool that was designed to execute various vulnerabilities automatically. Helpful for Penetration Testing



## Introduction

## Hostname

| IP Addresses | Network                |
| ------------ | ---------------------- |
| 10.0.5.250   | fw-rivendell.shire.org |
| 10.0.6.51    | Boromir                |

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Box Nslookup</p></figcaption></figure>

### Vulnerabilities&#x20;

> #### Default Password Use

*   Severity (High WordPress Admin Access)

    > This vulnerability allows for easy login to the administrative account. Administrative accounts have access to lots of potentially harmful information if leaked.&#x20;
*   Mitigation

    > Accounts that have overarching access need to have strong passwords and if possible 2FA. It is best, especially for administrative accounts, to make sure the password is not common. A passphrase is typically recommended.&#x20;

> #### Using WordPress - (WordPress web terminal)

*   Severity (High WordPress Web terminal)

    > WordPress is notorious for having tons of vulnerabilities and issues. The one exploited in this pentest was a plugin installed that gives terminal access to the computer.&#x20;
*   Mitigation

    > The best mitigation for this is to not use WordPress. If that is not possible, lock down the admin account, put rules and or filters on what plugins are allowed.

> #### Local Password Storage - Plaintext password

*   Severity (High plaintext password)

    > This misconfiguration allows for attackers to easily acquire hard coded passwords or accounts. This pentest had two versions of this. A hard coded password for a user account and a password hash to a different user account.
*   Mitigation

    > It is better to use hashes instead for hard coding passwords, assuming they are necessary. Make sure to keep the password strong and as many characters as possible so it cannot be cracked.&#x20;

> #### Websvn 2.6.0 - Remote Code Execution (Unauthenticated)

*   Severity (High RCE)

    > This vulnerability gives the attacker remote code execution (a reverse shell) to the system.&#x20;
*   Mitigation

    > Updating websvn to the newest version will most likely fix this vulnerability if they patched it. If not, then an alternative solution is required.&#x20;

## Supporting Evidence

### Scanning and Enumeration - WordPress

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption><p>nslookup of the wordpress box</p></figcaption></figure>

The first step in the Pentest was to enumerate the network to find the target. This can be done, as seen above, with the nslookup command.&#x20;

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption><p>nmaps scan of target</p></figcaption></figure>

The next step is to further enumerate the target with a port scanner. The screenshot above shows the open services on the target. The screenshot below shows more details

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption><p>nmap scan with additional flags</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption><p>Wpscan</p></figcaption></figure>

Another enumeration tool is wpscan. We can see that a wordpress server is running in the previous screenshot. We can use wpscan to enumerate the web server. Next, attempt to navigate the website.&#x20;

<figure><img src="../.gitbook/assets/image (4) (2).png" alt=""><figcaption><p>Website</p></figcaption></figure>

The screenshot above shows the landing space for the website. Because we know its WordPress, we can try and login to the admin page. http://site/wp-admin

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>Admin login</p></figcaption></figure>

### Initial Compromise / Foothold&#x20;

The account uses a set of default credentials to login to the admin account:

**Username**: Admin\_02

**Password**: admin1234

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption><p>Admin Panel</p></figcaption></figure>

Once access to the WordPress admin page is a terminal plugin that can be installed. This can be seen above and below.&#x20;

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption><p>local user password</p></figcaption></figure>

While in the terminal you can search through config files and find a database with a local user password stored in plaintext. Using this will give direct SSH access to the target.&#x20;

**username**: Elrond

**password** elrond77

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption><p>logging in as elrond on the wordpress target</p></figcaption></figure>

The screenshot above shows login to the target machine. The screenshot below shows the user flag for Elrond.&#x20;

<figure><img src="../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

### Enumeration - Boromir

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption><p>First target IP</p></figcaption></figure>

The first step in enumerating the Boromir target is to determine what subnet it is on. a quick look at the IP of the WordPress machine shows us the new subnet of 10.0.6.x. There are a variety of ways to determine what is on the network. I chose to just ping the next IP.

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption><p>Pinging of Boromir </p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>attempted Curl of IP</p></figcaption></figure>

Next, in order to gain further information, the IP was curled to determine if there was anything running a web server. Luckily, there was. the screenshot above shows the html content. Knowing this, I setup proxy chains so I could navigate to the site and eventually exploit it.

#### Setting up ProxyChains

{% embed url="https://technotes.noahbeckman.com/v/sec480-pentest-2/useful-things/proxy-chains" %}
Instructions on how to setup ProxyChains
{% endembed %}

After setting proxy chains you can then nmap against the target and set a proxy in Firefox to navigate to the site

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>Boromir Site</p></figcaption></figure>

This website is using WebSVN 2.6.0. This is critical to the foothold. After searching google for a vulnerability the following unauthenticated RCE came up:

{% embed url="https://www.exploit-db.com/exploits/50042" %}
Vulnerability Code
{% endembed %}

There is an issue with this payload though. A few lines have to be edited for it to work. Adding print statements for feedback is helpful when debugging.&#x20;

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption><p>Changes to script</p></figcaption></figure>

The payload had to be changed to reflect the kali attacker machine for a reverse shell. Boromir also only allows 80 and 443 out so it needs to use 443. Lastly, there is an extra parent directory websvn that is used. Once inside the Boromir target credentials need to be found. A directory with Boromir's hash can be found in /etc/ called svn-auth-accounts then cracked with RockYou and Hashcat.&#x20;

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption><p>Boromir's Has dumbed from svn-auth-accounts</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>hashcatting the password (its and MD5 hash so hashcat type 1600)</p></figcaption></figure>

```
$apr1$/dPEVRIP$33jd0o1KAzXVVJaSPDwCV/:boromir1984
```

Now that we have the password, we can SSH to Boromir instead of using the reverse shell still using proxy chains. This gives us the user flag. This all can be seen below.&#x20;

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption><p>Logging into the boromir account</p></figcaption></figure>

**Username**: Boromir

**Password**: boromir1984

### Privilege Escalation&#x20;

The last step is to Privilege escalate. The priv. esc. for this target is an improper configuration and password reuse. The user can just use the command **su root** and Boromir's password to gain access.&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
