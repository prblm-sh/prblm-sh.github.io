### C Lang Format Functions

#### Format Function Family
| Function | Usage |
|:--------:|:---------|
|fprintf() | prints to a File Stream |
|printf()  | prints to stdout |
|sprintf() | prints into a string |
|snprintf() | prints into a string with length checking |
|vfprintf() | prints to file stream from a variable argument structure(?) |
|vprintf() | prints to stdout from va arg struct |
|vsprintf() | prints into a string from a va arg struct |
|vsnprintf() | prints into a string with length checking from a va arg struct |

#### Format Function Relatives
setproctitle - set argv[]
syslog() - output to the syslog facility
err*, verr* warn*, vwarn*, etc (?)


#### Format Specifiers
Format specifiers are denoted by the `%` character, and tell `C` which format to print.

|   Spec   |         Type         |
|:--------:|:--------------------:|
|    %c    |      Character       |
|    %d    |     Signed Char      |
|    %e    |       Sci.Not.       |
|    %f    |        Float         |
|    %g    |    Similar to %e     |
|   %hi    |   Signed Int short   |
|   %hu    |  Unsigned Int short  |
|    %i    |     Unsigned Int     |
|    %l    |         Long         |
|   %lf    |        double        |
|   %Lf    |     Long Double      |
|   %lu    |  Unsigned Int/Long   |
|   %lli   |      Long Long       |
|    %o    |        octal         |
|    %p    |       pointer        |
|    %s    |        string        |
|    %u    |     Unsigned Int     |
|    %x    |     hexadecimal      |
|    %n    | loads value, does not print |
|    %%    |   prints `%` char    |

It should be noted that `\` is an escape character, and the sequence following the character (the escape sequence) will be replaced at compile time. For instance, `printf("\x25d\n"` will print the `%` char itself, as the `0x25` hex value translates to the `37` ASCII code, the code for `%`. While separate concepts in `C`, escape chars and sequences are often mistaken as necessary in format strings, despite them having no direct implications in their function, aside from abusing another programmers mistakes.

#cLanguage #formatFunction 