### Mod 26
We're given the flag string after it has been passed through a ROT13 cipher.
The ROT13 cipher is based on the there being 26 letters in the alphabet. Every letter is assigned a number, (a is 1, b is 2, etc.), and the text to be encrypted is 'rotated' by 13. A becomes N, B becomes O, etc. To decrypt the text, the same rotation is applied.

This cipher is available on numerous websites, or can be done quickly in python using the `codecs` library.

Launch the [python3Dev]({% link notes/python3Dev.md%}) command line REPL
`python3`
In the REPL, import the `codecs` library
`import codecs`
Set a variable to hold the text to en/decoded
`mod = "cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_GYpXOHqX}"`
Print the en/decoded text
`print(codecs.encode(mod, 'rot_13'))`

#picoCTF #python #encryption #cryptography #rot13