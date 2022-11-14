# Static Ain't Always Noise
## Challenge Description
Can you look at the data in this binary: [static](https://mercury.picoctf.net/static/ff4e569d6b49b92d090796d4631a2577/static)? This [BASH script](https://mercury.picoctf.net/static/ff4e569d6b49b92d090796d4631a2577/ltdis.sh) might help!


## Notes
Download the binary, run `strings static | grep -i picoCTF`
The output is the flag. 

Alternatively, run `chmod +x ltdis.sh static` which will also run strings on the binary, as well as `objdump`. The flag can then be found in the resulting `static.ltdis.strings.txt` file the bash script outputs. 