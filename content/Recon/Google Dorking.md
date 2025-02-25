---
title: Google Dorking
draft: false
tags:
  - recon
  - googledorking
---
## Summery
Google Dorking is the act of essentially being better at Googling things. If you know how to do it efficiently then you can get some useful bits of data.

## Syntax

#### Operators

| Operator | Summary     | Example                                                 |
| -------- | ----------- | ------------------------------------------------------- |
| \|       | OR operator | cats.com \| dogs.com (Returns results from either site) |
| *        | Wildcard    | *.cats.com (Grab all subdomains)                        |
| -        | Exclude     | -www (will exclude any www results)                     |

#### Filters

| Filter   | Summary                                                   | Example                                                        |
| -------- | --------------------------------------------------------- | -------------------------------------------------------------- |
| site:    | Narrows down results to the specified site                | site:cats.com                                                  |
| ext:     | Searches for a file type                                  | ext:js (Will show javascript files)                            |
| inurl:   | Searches for the specified value in the URL               | inurl:login (Will search a url for login. like cats.com/login) |
| intitle: | Searches for a value inside the title of a page           | intitle:cats                                                   |
| intext:  | filters the results with pages with the value in the page | intext:cats                                                    |

## Useful examples

