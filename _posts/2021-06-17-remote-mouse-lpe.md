---
title: Exploiting CVE-2021-35448
tags: [Research]
style: fill
color: danger
comments: true
description: Local Privilege Escalation via Remote Mouse GUI 3.008
---

## Introduction

I've come across a Local Privilege Escalation vulnerability in **Remote Mouse 3.008**

After installation remote mouse runs with `Administrator Privileges` on startup by default as it binds to local ports to listen for incoming connections.

## Testing Environment

This has been tested on `Windows 10 Pro 21H1` with `Remote Mouse 3.008`

Vulnerable app can be downloaded from here [https://www.exploit-db.com/exploits/50047](https://www.exploit-db.com/exploits/50047)

## Steps to Reproduce

***Note:*** 
- Remote Mouse must be installed as administrator 
- Local access is required in order to exploit this vulnerability.

Check current user privileges

![whoami](https://i.imgur.com/YIvWm7a.png)

Try opening remote mouse via desktop shortcut

![shortcut](https://i.imgur.com/jzcLQB6.png)

UAC (user account control) prompt will appear

![uac](https://i.imgur.com/kVdMVbV.png)

Since we don't have admin privileges/credentials we cant use this but Remote Mouse can be opened from system tray

![system tray](https://i.imgur.com/GgKxF2x.png)

Go to "Settings"

![settings](https://i.imgur.com/IPZMXAK.png)

Click on "Change..." in "Image Transfer Folder" section

![change](https://i.imgur.com/KWSAPDC.png)

"Save As" prompt will appear

![save as](https://i.imgur.com/4sLzJqM.png)

Enter `C:\Windows\System32\cmd.exe` in the address bar

![address](https://i.imgur.com/adq4bzH.png)

A new command prompt will be spawned with `Administrator` privileges

![cmd](https://i.imgur.com/CvCNcgZ.png)

***Update:*** CVE-2021-35448 has been assigned.

## References

- [https://www.exploit-db.com/exploits/50047](https://www.exploit-db.com/exploits/50047)
- [https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-35448](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-35448)
- [https://www.remotemouse.net/](https://www.remotemouse.net/)
