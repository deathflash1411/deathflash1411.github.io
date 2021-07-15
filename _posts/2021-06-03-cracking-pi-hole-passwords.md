---
title: Cracking pi-hole passwords
tags: [Research]
style: outline
color: primary
comments: true
description: How to crack pi-hole password hashes
---

## Introduction

During a recent pentest I came across a `pi-hole` instance, which was vulnerable to an authenticated remote code execution exploit.

## Problem

I didn't have any credentials at the moment to proceed further and escalate my privileges.

## Discovery

I started digging around the system since I already had local access.

`/etc/pihole/setupVars.conf` was interesting since it contained the web password hash.

`bccabd84061a09bccc5d3137f045c082dd4181a838f6b5774d8f5265c16cdd69`

![setupVars](https://i.imgur.com/ocoZrYc.png)

I tried identifying the hash with `hash-identifier`.

![hash-identifier](https://i.imgur.com/svOm7Ik.png)

`SHA-256` and `Haval-256` are the possible hash types.

I tried cracking the hash using `hashcat` with different `sha256` hash modes as listed on [https://hashcat.net/wiki/doku.php?id=hashcat](https://hashcat.net/wiki/doku.php?id=hashcat) but nothing worked.

After a little bit of googling I came across this issue on github: [https://github.com/pi-hole/pi-hole/issues/2521](https://github.com/pi-hole/pi-hole/issues/2521) **(Insecure password hashing: salt needed)**.

After reading the comments, I finally understood that passwords are simply hashed twice.

![comment](https://i.imgur.com/lvLMhjA.png)

Let's say "Salman" is the password string, then `pi-hole` password hash = `sha256(sha256("Salman"))`

Hmm, interesting so that's the reason `hashcat` couldn't crack it earlier since it's hashed twice.

### MDXfind

After a bit more of googling, I stumbled upon a tool called [mdxfind](https://www.techsolvency.com/pub/bin/mdxfind/).

*"MDXfind is a program which allows you to run large numbers of unsolved hashes, using many algorithms, against large numbers of plaintext words, very quickly."* - Waffle 

Well this is what I need, I downloaded the standalone binary (mdxfind.static)

![mdxfind.static](https://i.imgur.com/z33rZII.png)

`wget https://www.techsolvency.com/pub/bin/mdxfind/mdxfind.static -O mdxfind && chmod +x mdxfind`

I found this [blog](https://0xln.pw/MDXfindbible), that explained the usage of `MDXfind`, we know that the hash is not salted from github issue's comments.

![generating](https://i.imgur.com/oSbjNJj.png)

This looks like what we need, but we need to do some slight modifications as below:

1. Since we don't know the password string we'll remove the `echo -n 'Password'` from the command and use a wordlist instead. 

2. We know the hash type so we'll replace `-h 'ALL'` with `-h 'SHA256'`.
3. We know that it's not an md5 hash so we'll remove `!md5x`.
4. We know that it's double hashing so we'll change the number of iterations to 2 `-i 2`.
5. Finally we'll grep out out password hash `bccabd84061a09bccc5d3137f045c082dd4181a838f6b5774d8f5265c16cdd69`.

Here's the final command

`./mdxfind -h 'SHA256' -h '!salt,!user' -z -f /dev/null -i 2 stdin 2>&1 /usr/share/wordlists/rockyou.txt | grep 'bccabd84061a09bccc5d3137f045c082dd4181a838f6b5774d8f5265c16cdd69'`

![cracked](https://i.imgur.com/LbZlSGi.png)

BOOM! we cracked the `pi-hole` password hash 

## References

- [https://github.com/pi-hole/pi-hole/issues/2521](https://github.com/pi-hole/pi-hole/issues/2521)
- [https://emn178.github.io/online-tools/sha256.html](https://emn178.github.io/online-tools/sha256.html)
- [https://www.techsolvency.com/pub/bin/mdxfind/](https://www.techsolvency.com/pub/bin/mdxfind/)
- [https://0xln.pw/MDXfindbible](https://0xln.pw/MDXfindbible)
