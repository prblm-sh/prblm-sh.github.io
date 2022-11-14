# keygenme-py
## Challenge Description
[keygenme-trial.py](https://mercury.picoctf.net/static/b016c61bd2cc0be05a59da1dde67a2ac/keygenme-trial.py)
(No Hints)

## File Output
``` bash
❯ python keygenme-trial.py 

===============================================
Welcome to the Arcane Calculator, GOUGH!

This is the trial version of Arcane Calculator.
The full version may be purchased in person near
the galactic center of the Milky Way galaxy. 
Available while supplies last!
=====================================================


___Arcane Calculator___

Menu:
(a) Estimate Astral Projection Mana Burn
(b) [LOCKED] Estimate Astral Slingshot Approach Vector
(c) Enter License Key
(d) Exit Arcane Calculator
What would you like to do, GOUGH (a/b/c/d)? 
```

## Notes
Yeah fuck downloading via the web and then moving files, just run `wget` from the current challenge directory. 

Anyway, we get a python script.
Going by the comments in the file, the "dynamic part" of the key generation has not been verified to work. There's also something going on with a trial_user and also a btrial_user, I'm not sure of the significance at the moment. 
At the bottom of the script is a base64 encoded (sort of lmao) copy of the supposed entire "full keygen."

Username is "GOUGH" which gets fed into the b64 decode, I'm guessing the additional bUsername_trial = b"GOUGH" is to make it 8 characters to cooperate with b64???


Option b of the menu for the script points to a SQL database, which can be found near the beginning of the script.

Route A: reverse the full version unlock key, "unlock" the full version from there, find flag in full version.

Route 1: reverse/crack the "full-version" base64 at the end, find flag in full version

Route U38: break/abuse the SQL database and find the flag within there? quite possibly a red herring though. 