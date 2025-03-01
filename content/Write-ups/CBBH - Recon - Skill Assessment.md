---
title: CBBH - Recon - Skill Assessment
draft: 
tags:
  - recon
---

## Summary
Once we have the target IP set to the inlanefreight.htb we then get to start the fun bit of recon. Since I want to treat this like doing actual recon I am going to note down everything I acquire then I am going to try to answer the questions.


## Step 1: Grab the Subdomains

Since we know our domain is inlanefreight.htb and this is the only Apex domain we have to work with then we want to hunt down sub domains. For this we will use gobuster since thats what we used in the learning material

```bash
gobuster vhost -u http://inlanefreight.htb:50640 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

Probably could use a smaller list but always good to be thorough.

One million years later we will get this subdomain
![[Screenshot 2025-02-28 at 8.45.07 PM.png]]

It was at the end of a million entry list.... And since subdomains can nest we will want to iterate over web1337.inlanefreight.htb to see if we have a subdomain under this after we add it to /etc/hosts

We will just run the same gobuster script on each subdomain. And after the second iteration we have discovered the following subdomain

![[Screenshot 2025-02-28 at 9.09.16 PM.png]]

And we are going to keep doing this until this insanely large list outputs no more results. Luckily that was the last one as I was getting a bit bored

So now we know we have the two subdomains and the apex
inlanefreight.htb
web1337.inlanefreight.htb
dev.web1337.inlanefreight.htb
## Fingerprinting

Since these are vhosts I am assuming they are using the same server. However, for ritual reasons I will finger print all subdomains and the apex.

![[Screenshot 2025-02-28 at 9.39.05 PM.png]]

Guess what? they were indeed all the same.

Now lets crawl.

## Crawling

So now we are going to crawl across all of our instance with Recon Spider like we see below:

```bash
python3 ReconSpider.py http://inlanefreight.htb:506460
```

All except one were empty.

## Manual look over

Since I can not think of any other automated ways to do recon then to look for more domains. I am going to manually check endpoints.

inlanefreight.htb has nothing and is a little boring

web1337.inlanefreight.htb has some interesting info on its home page.

dev.web1337.inlanefreight.htb actually had a robots.txt file with useful info and a maing page that was just annoying

## Wrapping up

From here the only other recon I could think to do all involves getting domains. So I will check the questions and see if I can answer them.

I can so this wrap up is concluded