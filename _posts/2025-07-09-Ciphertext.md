---
title: Cipher's Secret Message
description: Sharpen your cryptography skills by analyzing code to get the flag.
author: xFraylin
date: 2025-07-09 01:14:00 +0800
categories: [Tryhackme]
tags: [CTF]
image:
  path: https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/Ciphertext/title.png?raw=true
---

## Introduction
Let's read the content
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/Ciphertext/content.jpg?raw=true){: width="1200" height="630" }
As you can see, we have to decrypt that to retrieve the flag

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/Ciphertext/cod.jpg?raw=true) 
We can see the whole code here, we have to modifiy the code to get the flag

```bash
from secret import FLAG

def enc(plaintext):
    return "".join(
        chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) + i) % 26 + base) 
        if c.isalpha() else c
        for i, c in enumerate(plaintext)
    )

with open("message.txt", "w") as f:
    f.write(enc(FLAG))
```
>
1. enc() applies a Caesar cipher with incremental shift (+i)
2. . + i adds the character's index, creating a progressive shift per character
3. chr(...) converts the resulting number back into a ciphered character.
{: .prompt-info }
Once we know what does the code do. we can procede to modify it
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/Ciphertext/modi.jpg?raw=true)
As you can see I just edit the variable and the hypertext to ciphertext. but when I tried to execute it, there is an error, 

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/Ciphertext/faildecrypt.jpg?raw=true)
> 
But the problem here is cause, I just forgot to put the variable decrypt next to def, we can modify it just adding decrypt
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/Ciphertext/decrypt.jpg?raw=true)

``` bash
message = "a_up4qr_kaiaf0_bujktaz_qm_su4ux_cpbq_ETZ_rhrudm"

def decrypt(cyphertext):
    return "".join(
        chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) + i) % 26 + base) 
        if c.isalpha() else c
        for i, c in enumerate(cyphertext)
    )

print("THM{" + decrypt(message) + "}")
```
>
I added the `message` variable manually with the `encrypted` string provided in the challenge.

I created a new function called `decrypt()` (instead of `enc())` and changed the logic:

I replaced `+ i` with `- i` to reverse the Caesar incremental shift.

This essentially undoes the encryption applied originally in enc().

Finally, I used `print("THM{" + decrypt(message) + "}")` to wrap the output in the expected flag format.
{: .prompt-info }

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/Ciphertext/done.jpg?raw=true)
AND HERE IS THE FLAG

>
YOU HAVE TO KNOW THAT IS THERE IS A GRAMATICAL ERROR IS CAUSE IM LEARNING ENGLISH, THANK YOU
{: .prompt-info }