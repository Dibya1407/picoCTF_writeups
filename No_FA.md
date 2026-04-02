---
title: "No FA"
date: 2026-04-01
layout: writeup
platform: PicoCTF
categories: [Web Exploitation]
tags: [web]
difficulty: medium
challenge link: https://play.picoctf.org/practice/challenge/765?category=1&difficulty=2&originalEvent=79&page=1
---

# No FA

Writeup for No FA challenge of PicoCTF 2026.

## admin password

Upon inspecting the provided users.db file using sqlite3 viewer, the users table was found to contain 20 entries. Each entry consisted of a username and a SHA256 hashed password.

The admin entry was identified:

```
Username: admin

Hash: c20fa16907343eef642d10f0bdb81bf629e6aaf6c906f26eabda079ca9e5ab67
```

the hash can be cracked using crackstation.net, which gives us the password as "apple@123"

## 2 Factor Authentication

After logging in, we are askeed for an OTP which lasts for 2 minutes. Hence any bruteforce attempts didn't work.

Upon inspecting the session cookie, we get a flask session cookie which upon decoding contains the otp.

for example - 
>.eJwty0sKgCAQANC7zFoCs1HzMiE5ieAPtVV091y0ffAeiMV7cmDgsrETMCijHp3ORmOiWLX8bYREfdhUwXClkKNEJRa9qV0jMrg7tWwTzWRdChneD0gDHHM.ac69XQ.GT-mNNjb11Ydu4LDYF-9w-pGSLc

decoding using https://www.kirsle.net/wizards/flask-session.cgi

we get -

``` 
{
    "logged": "false",
    "otp_secret": "3286",
    "otp_timestamp": 1775156573.8479855,
    "username": "admin"
}
```

the otp can be used to login and get the flag.

## Flag

<details>
  <summary>Show flag</summary>
  
  > `picoCTF{n0_r4t3_n0_4uth_9617ed73}`
</details>
