# Intro
[PicoCTF](https://picoctf.org/) is a 'capture-the-flag' computer security competition hosted by Carnegie Mellon University. While there is a timed competition that takes place typically once a year, the CTF is available for free year round. 

Create an account and login, then navigate to the "Pico Gym" by clicking the [Practice](https://play.picoctf.org/practice) link at the top of the page.  Here you'll find the entire list of currently available challenges, listed by points is descending order. The more points a challenge is worth, generally the more difficult it will be. 

Challenges are broken down into seven (7) categories:
- [ ] [Web Exploitation](https://play.picoctf.org/practice?category=1&page=1)
- [ ] [Cryptography](https://play.picoctf.org/practice?category=2&page=1)
- [ ] [Reverse Engineering](https://play.picoctf.org/practice?category=3&page=1)
- [ ] [Forensics](https://play.picoctf.org/practice?category=4&page=1)
- [ ] [General Skills](https://play.picoctf.org/practice?category=5&page=1)
- [ ] [Binary Exploitation](https://play.picoctf.org/practice?category=6&page=1)

Be sure to read through the [Pico Primer](https://primer.picoctf.com/), it serves as a very solid jumping off point for any challenge category, including explanations and examples of the underlying technology. Along with the [Resources](https://picoctf.org/resources) page provided by PicoCTF, you should be in great shape to get started finding flags. 

Challenges can be completed in any order, if you find yourself hitting the same wall on one, switch gears to a different category and come back to it later. Remember to take notes as you go, it will make jumping back in significantly easier, as well as help you keep track of every bit of information you pickup from a challenge, especially when the challenges involve reading through a few hundred lines of code. 

Challenges are completed by finding the flag in the challenge, then submitting the flag text into the challenge box.
Flags are always in the format of " picoctf{moreflagtexthere} ", always starting with `picoCTF`, without the surrounding quotes. If you have found a flag in the correct format, but it won't submit, make sure you've copied over the entire text, inlcuding the ending curly-bracket. 

#  Environment Setup
We only need a minimal setup to get started. If you have a linux install of any kind (running it on your desktop, a cloud-hosted VPS you have SSH access to, virtual machine of any sort, etc.) you're all ready to get started. Open up a terminal and pick a challenge!

If you don't have access to a linux environment that you can make changes to, picoCTF provides a built-in webshell accessible from the top right of the page. Click the "❯\_Webshell" rectangle and enter your username, and then your password.
Note: if you're new to linux environments, you will not see any characters while typing your password in, this is the standard behavior of unix terminals going back to the dawn of time.
Once you're logged into the webshell, you can proceed with any challenge you like. 

# Challenge workflow
While you may find or already have an approach that works for you, I like to keep every challenge separate from each other. This means that each challenge gets it's own folder in a parent 'picoCTF' folder for challenge files, and each gets their own note.

In order to stream line this, to start a challenge I right-click -> 'Copy Link' on the download link for file(s) for the challenge, and then run `wget https://mercury.picoctf.net/static/fb851c1858cc762bd4eed569013d7f00/flag` (this is the file for the "Obedient Cat" challenge) after pasting the link into the terminal. This should be doable with either another right click, or using `shift + control + V`. Doing so downloads the file directly to the folder you're shell is currently in, and allows you to skip having to move files from your downloads directory to the directory you'll actually be working in. 

If you're using the webshell, be sure to run `cat README.txt` to further familiarize yourself with environment, as well as some more helpful tips specific to completing challenges.

Remember, in linux everything (no seriously, everything) is treated as a file, and filenames are always case-sensitive. Also, hitting the "TAB" key on your keyboard will auto-complete filenames and commands, this *will* be a saving grace in more than one challenge. 

# My Setup
I'm running through the challenges from a Fedora 35 linux install, using the ZSH shell (as opposed to the default bash shell on most distros), with tilix for my terminal emulator. These details *should* be almost entirely irrelevant, but I'll attempt to keep note of idiosyncrasies related to them should they occur. 

# First Challenge
The "first" challenge is named "Obedient Cat," worth 5 points. To start, create a new folder called picoCTF/obedientCat (spaces in filenames can cause a lot of headaches on linux systems) using `mkdir -p picoCTF/obedientCat`. This will create a new directory called 'picoCTF' if it doesn't already exist (the `-p` command flag) and then a sub-directory named 'obedientCat'. Use the `cd` (change directory) command to move into the new directory (`cd picoCTF/obedientCat`). Click on the challenge square, read the description, and use `wget` to download the file (right-click and select copy the download link from the menu, then paste the link into your terminal), in this case `wget https://mercury.picoctf.net/static/fb851c1858cc762bd4eed569013d7f00/flag`.

Once you've found the flag, copy the text and submit it in the challenge box. You'll be notified if the flag text is correct, and your score in the upper-right corner will increase.

# Fair Play
Remember, the point of this, and every other CTF, is for *you* to learn. While I'm writing this with the intent of being a step-by-step explanation of how to complete various challenges, you will learn much, *much more* solving the challenges on your own, and only using write-ups like this to help point you in the right direction, or figure out if you're going down the entirely wrong rabbit-hole.