---
layout: post
title: Obedient Cat
author: A. Problem
permalink: obedientcat.html
---


|     Name     |    Category    | Points | Hints |
|:------------:|:--------------:|:------:|:-----:|
| Obedient Cat | General Skills |   5    |   3   |


[Link to Challenge](https://play.picoctf.org/practice/challenge/147)

## Description
Author: syreal

This file has a flag in plain sight (aka "in-the-clear"). [Download Challenge File](https://mercury.picoctf.net/static/fb851c1858cc762bd4eed569013d7f00/flag).

## Tools Used
```bash
$ wget
$ cat
$ bat
```


## Notes
The flag is in plain-text (aka, human readable, not obfuscated nor encrypted, or some weird proprietary binary format). In order to get the flag, we can use the standard `cat` command, or my preference `bat`, which is a clone of the old cat command rewritten in rust with the addition of printing line numbers and git-integration built in. While the extra output can sometimes cause issues when piping commands, I find it helpful more often than not.

## Solution
To print the flag to stdout (aka, your terminal), run `cat obedientCat_flag` and then copy the flag (" picoCTF{s4nity_thisisnottherealFlag}") into the text box in the challenge pop-up. If the flag is correct, you'll get a notification from the top of the page congratulating you on solving the challenge. 

## bat vs cat
To demonstrate the difference between the two commands, here's the (flag obfuscated) output of when using both commands.
### cat
```bash
❯ cat obediantCat_flag 
picoCTF{s4n1ty_NotTHeRestOfTheFlag}
```
### bat
```bash
❯ bat obediantCat_flag 
───────┬─────────────────────────────────────────────────────────
       │ File: obediantCat_flag
───────┼─────────────────────────────────────────────────────────
   1   │ picoCTF{s4n1ty_NotTHeRestOfTheFlag}
───────┴─────────────────────────────────────────────────────────
```

## References
[wget linux manual page](https://linux.die.net/man/1/wget)

[$ Bat GitHub Page](https://github.com/sharkdp/bat)

[cat linux manual page](https://man7.org/linux/man-pages/man1/cat.1.html)

man pages can also be accessed for most tools from the command line/terminal by running the `man` command, i.e.
```bash
man cat

man wget
```