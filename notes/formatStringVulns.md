### Format String Vulnerabilities

#### Overview
Format functions in `C` handle ANSI text with a variable number of arguments, most important being the `format specifier`. When a function evaluates the format string, it accesses the extra parameters given to the function. In doing so, primitive `C` data types can be represented in a (semi-)human readable string format to process user input, print error messages, info, etc. See [cFormatFunctions]({% link notes/cFormatFunctions.md%}).

If a user/attacker can send a `format string` to an ANSI C format function, the vulnerability is likely present. If the format string can be altered or controlled, the behavior of the function can be also be altered.
See [cFormatFunctions#Format Strings Specifiers]({% link notes/cFormatFunctions.md%}) for a table of format strings.

#### Example Code
Vulnerable:
``` C
int
func (char *user);
{
	printf(user);
}
```
"Safe:"
``` C
int
func (char *user);
{
	printf("%s", user);
}
```
In both examples, the `user` string is supplied by a potential attacker. In the vulnerable example, the function *does not* contain any format strings, which allows the attacker to supply their own chosen format string, and thus control the output of the function.
In the "safe" example, the `"%s"` format string is supplied in the function, which is the format string that tells `C` to interpret it as a `string` datatype at compile time.

It should be noted that `\` is an escape character, and the sequence following the character (the escape sequence) will be replaced at compile time. For instance, `printf("\x25d\n"` will print the `%` char itself, as the `0x25` hex value translates to the `37` ASCII code, the code for `%`. While separate concepts in `C`, escape chars and sequences are often mistaken as necessary in format strings, despite them having no direct implications in their function, aside from abusing another programmers mistakes.

#### Exploitation
Due to String Format Vulns allowing (in theory) to write to any address in memory, there's a wide range of options for exploitation.

#### Crashing the Program
The `%s` specifier displays memory from an address in the stack. Because of this, by sending multiple `%s` specifiers to the function, we can force an attempt to read an `illegal address` that's not actually mapped in memory, and typically causes the program to crash. Sometimes `%n` can be used as well to write to an address several times, however the `%n` specifier is often disabled and not available in modern programs.

While sometimes of limited usefulness, valuable information may be found in a core-dump after the crash. It can also be useful to prevent specific network services from responding (e.g. to impersonate them) or to take a system daemon offline.

#### Viewing Process Memory
If we can see the output of the format function, we can directly gather information about the function, view memory in the stack, and help find the correct offsets for full exploitation. 



##### References
[Exploiting Format String Vulns](https://www.win.tue.nl/~aeb/linux/hh/formats-teso.html)

#cLanguage #formatStringVuln #cExploit 
