---
layout: post
title: 
author: A. Problem
---

# Static Ain't Always Noise

|           Name            |    Category    | Points | Hints |
|:-------------------------:|:--------------:|:------:|:-----:|
| Static Ain't Always Noise | General Skills |   20   | none  |

[Link To challenge](https://play.picoctf.org/practice/challenge/163)

## Description
Author: syreal

Can you look at the data in this binary: [static](https://mercury.picoctf.net/static/ff4e569d6b49b92d090796d4631a2577/static)? This [BASH script](https://mercury.picoctf.net/static/ff4e569d6b49b92d090796d4631a2577/ltdis.sh) might help!

Note: Challenge files can be downloaded via the links above
## Tools Used
```bash
$ wget
$ file
$ objdump
$ strings
$ grep
$ chmod
```

## Notes
We're given two files for this challenge; a binary file named `static`, and a `bash` script named `ltdis.sh`.
Use the `file` command to find out what type of files we have.
```bash
❯ file ltdis.sh 
ltdis.sh: Bourne-Again shell script, ASCII text executable

❯ file static
static: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=17ad46e6c58b7c40148a89923e314662595d101b, not stripped
```

Open up the bash script in you're preferred text editor. The script is helpfully commented, and tells us that it takes the first argument given on the CLI (the `$1` variable in the script), and runs `objdump` with the disassemble-all option (`-D`) to convert the binary data and instructions to plaintext, and the `-j` to only display the named section of the binary, in this case the `.text` section. The script then sends the resulting hexdump to a file.

If the binary is succesfully disassembled and the new text file is created, the script runs `strings -a -t x` on the new text file. `strings` scans a given file, and returns all sets of printable characters at least 4 characters long. The `-a` option specifies reading the entire file (the default on most systems), and the `-t x` option makes strings print the hex offset of where the line was found in the binary. Other arguments for the `-t` option are `-t o` for octal offsets, and `-t d` for decimal format.

If you'd like to see the effects `objdump` of the options, you can run the commands below in your own terminal on the binary.
```bash
# run objdump dissasemble-all and redirect the output to a file
# the '>' character takes the output from stdout (your terminal) and redirects it, in this case sending the output to a file
objdump -D static > static-D.txt

# run objdump dissasemble-all, but only output the named section
objdump -Dj .text static > static-Dj.txt
```

Comparing the two files shows that the resulting `static-D.txt` file ends up being almost 900 lines of output, while the `static-Dj.txt` file is just short of 150.

With both the script and the binary downloaded, use `chmod +x` to add execution/run permissions to both files;
```bash
chmod +x ltdis.sh

chmod +x static
```

The files can now be run using `./static` and `./ltdis.sh`.
Running the `static` binary prints output that there is a flag- somewhere, and running the script by itself prints an error of disassembly failed, followed by a usage message telling the user to run the script with a binary file as an argument.


## Solutions
To solve the challenge, run the `ltdis.sh` script on the `static` binary;
```bash
./ltdish.sh static
```
A message with display telling the user the disassembled hex dump can be found in a new file `static.ltdis.x86_64.txt`, and any strings found in the resulting file have been printed to `static.ltdis.strings.txt`.

The flag can be found by going over the lines in the `static.ltdis.strings.txt`. Or, because the flag is always in the same format, starting with `picoCTF{`, we can use `grep` to search the file for the flag, with
```bash
# -i makes grep caseINsensitive
# the pattern to search for is the first argument
# followed by the file to search.
grep -i picoCTF{ static.ltdis.strings.txt
```
The returned output should be a single line of the hex offset, followed by the flag for the challenge.

### The really fast solution.
Alternatively, we can skip almost every step above and from the CLI with:
```bash
strings static | grep -i picoCTF{
```
This command runs `strings` on the binary, and pipes (sends directly, the `|` char) the output from strings into grep, which searches the incoming character stream, and only returns the lines that match the specified pattern.
The output of the above command should be just the flag itself.

## References
[strings Man Page](https://man7.org/linux/man-pages/man1/strings.1.html)

[obdjump Man Page](https://linux.die.net/man/1/objdump)

[file Man Page](https://linux.die.net/man/1/file)

[chmod Man Page](https://linux.die.net/man/1/chmod)

