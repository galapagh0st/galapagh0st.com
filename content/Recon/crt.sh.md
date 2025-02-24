---
title: crt.sh
draft: true
tags:
  - crtsh
  - recon
  - domains
---
## Summary
When conducting recon we should grab the apex domains and then those Apex's sub domains that are within scope. These will give us multiple different places to look for vulnerabilities. [[crt.sh ]] a


## Examples

#### Grabbing Domains Easily

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






###### Tags
#crtsh #recon #domains