# Tab, Tab, Attack
## Description
Using tabcomplete in the Terminal will add years to your life, esp. when dealing with long rambling directory structures and filenames: [Addadshashanammu.zip](https://mercury.picoctf.net/static/3afd18a65e42b80526aa87f9766c588b/Addadshashanammu.zip)

## Notes
Unzip the downloaded file with `unzip`, then type in the `cd` command, and contine pressing tab until you reach the final directory, `Ularradallaku`, you will find a ELF file named `fang-of-haynekhtnamet`.

The flag can be found by running `strings fang-of-haynekhtnamet | grep -i pico`

OR

running the binary with `./fang-of-haynekhtnamet`