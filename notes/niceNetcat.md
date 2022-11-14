### Nice Netcat
We're given a netcat command to run;
`nc mercury.picoctf.net 22092`
when run, it connects to the server and prints out 24 lines of [ASCII]({% link notes/ASCII.md%}) decimal codes, one on each line.
Run `nc mercury.picoctf.net 22902 >> netcatOutput.txt` to capture the response from the server to a file, after a moment hit `Ctrl + C` to stop the command, as the server does not automatically disconnect you.
We can use the Rapid Tables Converter linked below to convert the decimal bytes to human-readable ASCII text. Copy the output into the `decimal` text-box, and the flag will be printed out in the `ASCII` text box. 

TODO: Figure out how to do this via CLI/Scripting

#### References
[ASCII Table](https://www.rapidtables.com/code/text/ascii-table.html)
[Rapid Tables Converter](https://www.rapidtables.com/convert/number/ascii-hex-bin-dec-converter.html)


#picoCTF #netcat #ascii
