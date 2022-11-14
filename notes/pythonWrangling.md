### Python Wrangling
We're given 3 files, a python script `ende.py`, an encrypted version of the flag `flag.txt.en`, and a password for the python script `pw.txt`.

The flag file is encrypted using the [fernet]({% link notes/fernet.md%}) method. Reading the script, it takes either `-e` or `-d` args for encrypt/decrypt and the file to be en/decrypted. 

To get the flag, from the CLI run
`python3 ende.py -d flag.txt.end`
and enter the password from `pw.txt` when prompted.

#python #picoCTF 