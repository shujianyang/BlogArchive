---
title: VMware VMs Not Running Problem
date: 2017-06-03 15:00:00 -0600
---

### Problem

Today something weird happened on my VMware Workstation 12 Player. I was unable 
to run any of my virtual machines(VMs) stored on my computer. When I tried to
run a VM, an error message pop up, saying:

**VMware Player cannot connect to the virtual machine. Make sure you have rights 
to run the program, access all directories the program uses, and access all 
directories for temporary files.**

**Failed to connect pipe to virtual machine. The system cannot find the path specified.**

If start VMware Workstation Player in administrator mode, the second part of the
error message became:

**The vmx process exited prematurely.**
<!--excerpt-->


### Cause

Lots of attempts were performed based on the solutions found on Internet, 
including disableing anti-virus software, reintalling VMware Workstation. But 
none of the methods worked.

I then checked the Windows event log to see if I could find some useful information
there. Run the Event Viewer, go to Windows Logs - Application. Then I found an
application error record with the following message:

```nohighlight
Faulting application name: vmware-vmx.exe, version: 12.5.6.19378, time stamp: 0x591415b1
Faulting module name: gameudp.dll_unloaded, version: 6.0.7.73, time stamp: 0x5917a9d7
Exception code: 0xc0000005
Fault offset: 0x000000000001129b
Faulting process id: 0x1af4
Faulting application start time: 0x01d2dcc9362379ba
Faulting application path: E:\Program Files (x86)\VMware\VMware Player\x64\vmware-vmx.exe
Faulting module path: gameudp.dll
Report Id: 0e182361-bbb6-48d2-90f0-59e7256321c2
Faulting package full name: 
Faulting package-relative application ID: 
```

Combined with the previous error message displayed by VMware Workstation, I was
able to determine that the vmware-vmx.exe encountered some errors brought by
gameudp.dll while starting and was terminated.

### Solution

Further research about this gameudp.dll indicates that it is not a part of the 
original Windows system file and is safe to be removed. So I backed up this file,
then deleted it from C:\Windows\System32. After that, I restart VMware Workstation.
This time, the VM successfully started and the problem was solved.

Interestingly, later I restore the gameudp.dll back to its original location. The
VMware Workstation still functioned normally. The actual cause of the problem is
yet to be found and the role of gameudp.dll is still not clear.

Hope this article may help someone who has the same problem as I did!
