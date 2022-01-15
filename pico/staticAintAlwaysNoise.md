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
$ cat
$ reset
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

We can try to use `cat static` to printout the binary data to our terminal, however the output is by no means human-readable, your terminal likely has no idea what to do with it on it's own, and will give you a garbled mess of non-formatted lines like
```bash
����H����!    H�=�	 �
 ]����fDUH��]�f���UH��H�=�������]�f.�DAWAVI��AUATL�%F UH�-F SA��I��L)�H�H���W���H��t 1��L��L��D
```
that have essentially zero meaning to us. There's also a good chance of this screwing with your terminal prompt, it's advised to run `reset` in your terminal, which will refresh your terminal session and clear the garbage output.

The `ltdis.sh` script is human readable, and can be printed the terminal, but using a text editor (like [micro](https://micro-editor.github.io/)) makes the task easier.

Open up the `ltdis.sh` bash script in you're preferred text editor to inspect it before running. The script is helpfully commented, and tells us that it takes the first argument given on the CLI (the `$1` variable in the script), and runs `objdump` with the disassemble-all option (`-D`) to convert the binary data and instructions to plaintext, and the `-j` to only display the named section of the binary, in this case the `.text` section. The script then sends the resulting hexdump to a file.

If the binary is successfully disassembled and the new text file is created, the script runs `strings -a -t x` on the new text file. `strings` scans a given file, and returns all sets of printable characters at least 4 characters long. The `-a` option specifies reading the entire file (the default on most systems), and the `-t x` option makes strings print the hex offset of where the line was found in the binary. Other arguments for the `-t` option are `-t o` for octal offsets, and `-t d` for decimal format.

If you'd like to see the effects of the different options with `objdump`, you can run the commands below in your own terminal on the binary.
```bash
# run objdump dissasemble-all and redirect the output to a file
# the '>' character takes the output from stdout (your terminal) and redirects it to a new file.
objdump -D static > static-D.txt

# run objdump dissasemble-all, but only output the named section
objdump -Dj .text static > static-Dj.txt
```

Keep in mind, when using `>` redirects, a single `>` will overwrite the file if it already exists, and a double `>>` will append to the end of the file if it already exists. 

Comparing the two files shows that the resulting `static-D.txt` file ends up being almost 900 lines of output, while the `static-Dj.txt` file is just short of 150.

In order to run both the script and the binary, we have to change the `execution` permissions. You can see a current files permissions with `ls -l`. The permissions are displayed in the first column. If the last 'bit' on the right is `-`, the shell will refuse to run the file with a `permission denied` error.
```bash
-rw-r--r--. 1 prblm413 prblm413  785 Mar 15  2021 ltdis.sh
-rw-r--r--. 1 prblm413 prblm413 8376 Mar 15  2021 static
```

Use `chmod +x` to add execution/run permissions to both files;
```bash
chmod +x ltdis.sh

chmod +x static
```

After adding the execution permission, running `ls -l` again will show the files now have the execution permission bit set to `x`, which means the file is allowed to be run.
```bash
-rwxr-xr-x. 1 prblm413 prblm413  785 Mar 15  2021 ltdis.sh
-rwxr-xr-x. 1 prblm413 prblm413 8376 Mar 15  2021 static
```

The files can now be run using `./static` and `./ltdis.sh`.
Running the `static` binary prints output that there is a flag- somewhere, and running the script by itself prints an error of disassembly failed, followed by a usage message telling the user to run the script with a binary file as an argument.


## Solutions
To solve the challenge, run the `ltdis.sh` script on the `static` binary;
```bash
./ltdis.sh static
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

### One-Liner Solution.
Alternatively, we can skip every step above except downloading the files, and from the CLI run:
```bash
strings static | grep -i picoCTF{
```
This command runs `strings` on the binary, and pipes (sends directly, the `|` char) the output from strings into grep, which searches the incoming character stream, and only returns the lines that match the specified pattern.
The output of the above command should be just the flag itself.

## References
[strings Man Page](https://man7.org/linux/man-pages/man1/strings.1.html)

[grep Man Page](https://linux.die.net/man/1/grep)

[obdjump Man Page](https://linux.die.net/man/1/objdump)

[file Man Page](https://linux.die.net/man/1/file)

[chmod Man Page](https://linux.die.net/man/1/chmod)

