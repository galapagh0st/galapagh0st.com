---
title: FFUF
draft: 
tags:
  - recon
  - fuzzing
---
## Summary
FFUF is a command line Fuzzing tool that makes discovery and testing much easier. It is important to note if you run ffuf and see nothing then you should double check your command. Chances are it is just formatted wrong

## Examples

### Fuzzing a Directory
```bash
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ
```

the above command we see that -w is to se the following wordlist we will be using and the -u is for the URL we will be using

we can also see that the word list has :FUZZ appended to the end this is saying to replace in the URL where we put FUZZ with a value from our list

## Fuzzing for Webpages (extentions)

once you have some directories you can start fuzing for the webpages attached to them. You can find out what web pages exist by checking for the extension. Like you can see here:

```bash
ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
```

as you can see we look at the index since most endpoints will at least have an index page.

## Fuzzing Recursively

Fuzzing recursively allows you to dig deeper. In this case for sub directories. With a command like the following:

```bash
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v
```

The `-recursion` tag tells ffuf that we want to do a recursion scan `-recursion-depth 1` tells ffuf that we only want to go to a depth of one. `-e` is adding a comma separated extension. In this case we are only searching for one ending in PHP. `-v` is basic verbose answer.

NOTE: the last variable in a recursive ffuf call has to be FUZZ

## Fuzzing Subdomains

```bash
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/
```

Note: If you are fuzzing a locally hosted site you wont be able to get the subdomains from fuzzing them with FFUF because if it can not find it in the /etc/hosts file it will then go to public DNS records. So you will have to do Vhost fuzzing

## Vhost Fuzzing
Grabbing public domains is good and all but if a subdomain is not public you might be able to acquire it via Vhost fuzzing.

```bash
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'
```

We can see in the script above we are setting the host with the `-H` flag followed by the string `Host: FUZZ.academy.htb`

When we run this we will always get a 200 response back so we want to look for size from the response. To do that run it for a second and see what the average file size is coming back.

## GET Request Fuzzing

```bash
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:31830/admin/admin.php?FUZZ=key -fs 798 -v
```

## POST Request Fuzzing

```bash
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
```

`-X POST` tells ffuf that we are making a POST request
`-d 'FUZZ=key'` lets us pass it in the header
`-H 'Content-Type: application/x-www-form-urlencoded'` is needed to pass a value in the header.

## Clusterbomb Fuzzing

```bash
ffuf -request tmp.txt -request-proto http -mode clusterbomb -w /usr/share/seclists/Passwords/pass5.txt:FUZZPASSWORD -w /usr/share/seclists/Usernames/top-usernames-shortlist.txt:FUZZUSERNAME -fs 3256
```

Notice `-mode` sets the mode



#recon #fuzzing #ffuf