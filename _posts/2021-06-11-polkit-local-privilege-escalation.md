---
title: Exploiting CVE-2021-3560
tags: [Research]
style: fill
color: success
comments: true
description: Local privilege escalation via polkit
---

## Introduction

Local privilege escalation via `polkit` (CVE-2021-3560)

***Note:*** The vulnerability was originally discovered by [Kevin Backhouse](https://twitter.com/kevin_backhouse) from [GitHub Security Lab](https://twitter.com/GHSecurityLab). I've just written the code to automate the exploit.

## Testing Environment

The exploit has been tested on `Ubuntu-20.04.1-Desktop` with `policykit-1 0.105-26ubuntu1`

![version](https://i.imgur.com/MBfYklQ.png)

## Steps to Redproduce

Install Open SSH Server & start it

```
sudo apt update
sudo apt install -y openssh-server 
sudo systemctl start ssh
```

Login via ssh

```
ssh localhost
```

![ssh](https://i.imgur.com/xd0v5AY.png)

Download the exploit

```
git clone https://github.com/deathflash1411/CVE-2021-3560 && cd CVE-2021-3560
```

![git clone](https://i.imgur.com/M6A6JDO.png)

Run the exploit

```
python3 CVE-2021-3560.py
```

![exploit](https://i.imgur.com/trlHkzC.png)

A new user `flash` has been created with uid `1001`

Run the command displayed by the exploit

![command](https://i.imgur.com/SsHZ9x1.png)

Login as `flash` with password `flash` via su and spawn a root shell `sudo -i`

```
su - flash
```

![rooted](https://i.imgur.com/qRvp1WI.png)

***Note:*** The command might not work on the first try, run it a few times if you're unable to login.

## References

- [https://github.blog/2021-06-10-privilege-escalation-polkit-root-on-linux-with-bug/](https://github.blog/2021-06-10-privilege-escalation-polkit-root-on-linux-with-bug/)
- [https://www.youtube.com/watch?v=QZhz64yEd0g](https://www.youtube.com/watch?v=QZhz64yEd0g)
- [https://ubuntu.com/security/CVE-2021-3560](https://ubuntu.com/security/CVE-2021-3560)