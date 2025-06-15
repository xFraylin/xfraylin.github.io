---
title: IP-Geolocation
description: This is a sample script That you can get the geolocation with the ip address.
author: xFraylin
date: 2024-11-28 3:37:00 0800+
categories: [Bash]
tags: [script]
image:
  path: https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/ip.png?raw=true
---
### Introduction
Im going to do a basic script, that you can get the location of ip addresse's

```bash
#!/bin/bash
echo "Dame la ip que quieres geolocalizar"
read  ip
if [ -z "$ip" ]; then
    exit 1
fi
curl ipinfo.io/$ip?token=17ee73b03b28ca 
```

### Explanation
> 
1. `echo` This command displays text or values in the terminal
2. `read` This reads the data that the user type and store in the variable
3. `if [ -z "$ip" ]; then`, condition checks if the variable is  empty
4. `curl`  calls the ipinfo.io API with the IP provided by the user
{: .prompt-tip }

Usage, you have to save the script and give permition `chmod +x` and run

1. `./script.sh`

## Here is the basic code 
<https://github.com/xFraylin/iplocation>

You can copy the code, You can edit if you want.