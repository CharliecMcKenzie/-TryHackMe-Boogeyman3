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
