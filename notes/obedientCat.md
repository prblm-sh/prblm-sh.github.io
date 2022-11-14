# Obedient Cat
|     Name     |    Category    | Points | Hints |
|:------------:|:--------------:|:------:|:-----:|
| Obedient Cat | General Skills |   5    |   3   |
## Description
Author: syreal

This file has a flag in plain sight (aka "in-the-clear"). [Download flag](https://mercury.picoctf.net/static/fb851c1858cc762bd4eed569013d7f00/flag).

## Tools Used
`cat` [cat]({% link notes/cat.md%})
`bat` [bat]({% link notes/bat.md%})
`strings` [strings]({% link notes/strings.md%})

## Notes
The flag is in plain-text (aka, human readable, not obfuscated nor encrypted). In order to get the flag, we can use the standard `cat` command, or my preference `bat`, which is a clone of the old cat command rewritten in rust with the addition of printing line numbers and git-integration built in. While the extra output can sometimes cause issues when piping commands, I find it helpful more often than not.

To print the flag to stdout (So you can read it), run `cat obedientCat_flag` and then copy the flag (" picoCTF{s4nity_thisisnottherealFlag}") 