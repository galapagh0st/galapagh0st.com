---
title: crt.sh
draft: false
tags:
  - crtsh
  - recon
  - domains
---
## Summary
crt.sh is a tool that lets us grab APEX and Subdomains tied to a license. This is a passive way of doing recon

#### Examples

##### Grabbing Domains Easily

This following bash script should be saved as a command. However, it allows you to call to crt.sh with the domain given.
```bash
curl -s https://crt.sh/\?cn\=%.$1\&output=json | jq -r '.[].name_value' | sed 's/\*\.//g' | sort -u
```

The bash command should be like the following
```bash
crtsh cats.com
```

If you want just the Apex domains from the list you can run the following
```bash
cat file.txt | rev | cut -d *.* -f 1,2 | rev | sort -u > newfile.txt
```


### 



###### Tags
#crtsh #recon #domains