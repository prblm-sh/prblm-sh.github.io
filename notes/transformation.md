### Transformation - PicoCTF
We're given a file named `enc`, it's a UTF-8 encoded text file with a string of Japanese characters.
`灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸弲㘶㠴挲ぽ`
It's accompanied by the [python3Dev|python]({% link notes/python3Dev.md%}) snippet below;
```python
''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])
```
Which we can completely ignore.
The file itself is supposedly UTF-8, however the characters in said file are UTF-16E (16bit Big-Endian). Use `cat` to printout the contents of the file, then copy them. Go to [Rapid Tables](https://www.rapidtables.com/convert/number/ascii-hex-bin-dec-converter.html) and paste the characters into the ASCII box. Then, Copy the output in the `hex` box and save it somewhere. Reload the Rapid Tables page, then paste the *outputted* hex into the `Hex (bytes)` box, the flag for the challenge will be generated in the ASCII box. 


#reverseengineering #reveng #python
