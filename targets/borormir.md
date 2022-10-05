# Borormir

**DISCLAIMER**

This report is intended only for the use of the individual or entity to which it is addressed and may contain information that is privileged, confidential, and exempt from disclosure under applicable law. If the reader of this disclaimer is not the indented recipient, you are hereby notified that any dissemination, distribution, or copying of this document is strictly prohibited. If you receive this document in error, please notify the creator immediately by telephone and return the original document to the owner.

## Executive Summary

This report documents the findings of a penetration test carried out by Noah Beckman against the target Boromir. The project was completed in September 2022 and revealed the existence of two security-relevant issues. The completion of the assessment took a total of three days.&#x20;

The main target in scope was a computer running a WordPress Website. Methodology wise the assessment followed a black-box approach, meaning there was no access to the source code or file system. The Cyber.local team provided the environment and reset capabilities.

The aforementioned compromises involve two actual security vulnerabilities and multiple general weaknesses. The majority of these discoveries received a high severity ranking as root level compromise was possible as well as local file exploitation potentially disclosing PII information.&#x20;

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

    > This vulnerability allows for remote commands to be executed. /etc/passwd can be viewed as well as other commands can be run. This exploit allows for a reverse shell to be established.
*   Mitigation

    > The web form needs to validate inputs and maintain a whitelist of acceptable filenames. In addition, a corresponding identifier (not the actual name) to access the file should be used. Any request containing an invalid identifier can then simply be rejected. This is the [approach that OWASP recommends](https://www.owasp.org/index.php/Testing\_for\_Remote\_File\_Inclusion). Refer to the source below for more information on mitigation.

## Supporting Evidence

### Scanning and Enumeration - WordPress

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption><p>nslookup of the wordpress box</p></figcaption></figure>

The first step in the Pentest was to enumerate the network to find the target. This can be done, as seen above, with the nslookup command.&#x20;

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption><p>nmaps scan of target</p></figcaption></figure>

The next step is to further enumerate the target with a port scanner. The screenshot above shows the open services on the target. The screenshot below shows more details

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption><p>nmap scan with additional flags</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption><p>Wpscan</p></figcaption></figure>

Another enumeration tool is wpscan. We can see that a wordpress server is running in the previous screenshot. We can use wpscan to enumerate the web server. Next, attempt to navigate the website.&#x20;

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Website</p></figcaption></figure>

The screenshot above shows the landing space for the website. Because we know its WordPress, we can try and login to the admin page. http://site/wp-admin

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>Admin login</p></figcaption></figure>

### Initial Compromise / Foothold&#x20;

The account uses a set of default credentials to login to the admin account:

**Username**: Admin\_02

**Password**: admin1234

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>Admin Panel</p></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>attempted Curl of IP</p></figcaption></figure>

Next, in order to gain further information, the IP was curled to determine if there was anything running a web server. Luckily, there was. the screenshot above shows the html content. Knowing this, I setup proxy chains so I could navigate to the site and eventually exploit it.





<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

```
$apr1$/dPEVRIP$33jd0o1KAzXVVJaSPDwCV/:boromir1984
```

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>
