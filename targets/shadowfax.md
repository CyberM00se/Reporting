# ShadowFax

The aim of this Pentest is to assess the security of a clients endpoint Bifur. This report contains technical terms, but has been written so a non-technical reader with basic computing knowledge would understand it. Technical context will be provided in the appendices and screenshots. Should the reader encounter difficulties understanding any section of the report, reading the "Executive Summary" and the "Conclusions and Recommendations" section. For further help, contact the help department.&#x20;

### Some Definitions

**Hacker**: term given by the public that the cyber security industry more accurately call and attacker or intruder.

**Vulnerability:** Typically a bug or a misconfiguration in a computer program, or computer that can be abused to gain access on a computer

**Exploit:** A program or strategy to exploit a vulnerability.&#x20;

**Privilege Escalation:** A technique to escalate privileges on a computer, often to an admin user.

**Metasploit/Meterpreter:** A tool that was designed to execute various vulnerabilities automatically. Helpful for Penetration Testing

{% embed url="https://pentestreports.com/reports/BitesPenTesting/penetration-test-report.html" %}

## Hostname

| IP Addresses | Network    |
| ------------ | ---------- |
|              | .shire.org |

### Target Overview

### Vulnerabilities&#x20;

> #### Local File Inclusion

*   Severity (High Threat Directory Traversal)

    > This vulnerability allows for remote commands to be executed. /etc/passwd can be viewed as well as other commands can be run. This exploit allows for a reverse shell to be established.
*   Mitigation

    > The web form needs to validate inputs and maintain a whitelist of acceptable filenames. In addition, a corresponding identifier (not the actual name) to access the file should be used. Any request containing an invalid identifier can then simply be rejected. This is the [approach that OWASP recommends](https://www.owasp.org/index.php/Testing\_for\_Remote\_File\_Inclusion). Refer to the source below for more information on mitigation.

## Supporting Evidence

### Scanning and Enumeration

This is the live webpage active on the target. To enumerate further I inspected the webpage code using the network page and raw inspector. This showed me that there is a function being passed when the log files view file button is pressed. This can be seen in a screenshot below.

#### Vulnerability Detection

This section will detail how the initial vulnerability was found and exploited.

#### Foothold

#### Privilege Escalation

Privilege escalation typically starts out with searching through currently running services, SUID bits, and more. The program Linpeas.sh is typically another good start that automatically runs through most of the checks. Often times this gives us the best place to look not an actual vulnerability.&#x20;

#### Persistence



**Post Exploitation (Loot)**

{% embed url="https://app.gitbook.com/s/ARbxojkXZUSOB608QVbo/tools-and-loot/loot" %}
Loot Page
{% endembed %}

### Conclusions and Recommendations

In conclusion

### **Lab Issues**
