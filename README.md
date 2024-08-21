# TryHackMe Boogeyman 3 Challenge

## Introduction
Without tripping any security defences of Quick Logistics LLC, the Boogeyman was able to compromise one of the employees and stayed in the dark, waiting for the right moment to continue the attack. Using this initial email access, the threat actors attempted to expand the impact by targeting the CEO, Evan Hutchinson.  
<p align="center">
<img src="https://i.imgur.com/wTOKObl.png" height="80%" width="80%" alt="email"/>

The email appeared questionable, but Evan still opened the attachment despite the scepticism. After opening the attached document and seeing that nothing happened, Evan reported the phishing email to the security team.

### Initial Investigation
Upon receiving the phishing email report, the security team investigated the workstation of the CEO. During this activity, the team discovered the email attachment in the downloads folder of the victim.
<p align="center">
<img src="https://i.imgur.com/wSUSPEk.png" height="80%" width="80%" alt="downloads"/>

In addition, the security team also observed a file inside the ISO payload, as shown in the image below.
<p align="center">
<img src="https://i.imgur.com/qthDWXo.png" height="80%" width="80%" alt="iso"/>

## Walk-through:
I first set the time range in ELK to August 29 and August 30, 2023, and then searched for the email attachment ‘ProjectFinancialSummary.pdf’. This search revealed the PID associated with the attachment.
<p align="center">
<img src="https://i.imgur.com/hLfMd4y.png" height="80%" width="80%" alt="pdf"/>

The results also revealed the commands from the stage 1 payload, which attempted to implant a file in another location, establishing a persistence mechanism named "review." This implanted file was ultimately used and executed by the stage 1 payload. 
<p align="center">
<img src="https://i.imgur.com/POHgoSC.png" height="80%" width="80%" alt="commands"/>

By examining the surrounding logs, I discovered that the execution of the implanted file on the machine initiated a potential C2 connection. 
<p align="center">
<img src="https://i.imgur.com/oo2zjGI.png" height="80%" width="80%" alt="ip"/>

The attacker discovered that they had local administrator access. Tracing the actions of ‘review.bat’, we can see that the attacker used ‘fodhelper.exe’ to execute a UAC bypass.
<p align="center">
<img src="https://i.imgur.com/On3ua0W.png" height="80%" width="80%" alt="uac"/>
<img src="https://i.imgur.com/XfYZHir.png" height="80%" width="80%" alt="fodhelper"/>

Having a high privilege machine access, the attacker attempted to dump the credentials inside the machine. The attacker used a GitHub link to download a tool for credential dumping.
<p align="center">
<img src="https://i.imgur.com/PdOEgNe.png" height="80%" width="80%" alt="github"/>

Following the Mimikatz trail reveals that after successfully dumping credentials on the machine, the attacker used them to gain access to another system. With these new credentials, the attacker then attempted to enumerate accessible file shares. 
<p align="center">
<img src="https://i.imgur.com/NXKEaNR.png" height="80%" width="80%" alt="ps1"/>

Continuing to follow the Mimikatz trail reveals that after retrieving the contents of a remote file, the attacker used the new credentials to move laterally. The logs also show the hostname of the target machine during this lateral movement attempt. 
<p align="center">
<img src="https://i.imgur.com/jgYXVD6.png" height="80%" width="80%" alt="allan"/>

By filtering for ‘event.code 1’ using the hostname, we identified the parent process name of the malicious command that was executed. 
<p align="center">
<img src="https://i.imgur.com/3ZrW8If.png" height="80%" width="80%" alt="host.exe"/>

We also see that the attacker dumped the hashes on this second machine, revealing the username and hash of the newly acquired credentials. 
<p align="center">
<img src="https://i.imgur.com/5xAgrnw.png" height="80%" width="80%" alt="admin"/>

After gaining access to the domain controller, the attacker attempted to dump the hashes via a DCSync attack. The attacker dumped the ‘administrator’ and ‘backupda’ accounts.
<p align="center">
<img src="https://i.imgur.com/44EOAGm.png" height="80%" width="80%" alt="dcsync"/>

After dumping the hashes, the attacker attempted to download another remote file to execute ransomware.  
<p align="center">
<img src="https://i.imgur.com/60Diono.png" height="80%" width="80%" alt="ransomboogey"/>

## Summary
