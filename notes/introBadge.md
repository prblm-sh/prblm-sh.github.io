## Intro Badge
Exercises are typically completed one of two ways; either find a `key` in the form of a `UUID`, or succesfully run a command with a `UUID` generated for you attached to the command in some fashion. Each challenge *should* generate a unique-per-user instance to work on, which will be linked to in the challenge.

For the sake of pentesterlab resources, try to remember to destroy the instance after completing a challenge. 

### Intro 00

Get acquainted with the scoring process, visit the link and copy the key and submit it to score the exercise. 

[Challenge URL](https://pentesterlab.com/exercises/intro00/scoring)
Key: `a71c9797-961e-4d89-bc42-bb95ecadb757`

### Intro 01
Most websites have a `robots.txt` file that instructs web crawlers (i.e. google search) how to navigate the website.
Often the `Disallow` keyword is used to instruct a crawler *not* to visit certain pages.
As a human-attacker, this can be a neon sign of where potentially sensitive/useful information is stored.

[Challenge URL](https://pentesterlab.com/exercises/intro01/scoring)
Key: `c3e3314b-b383-452e-990b-829777e06b36`

### Intro 02
HTML comments, which look like `<!-- this is a comment -->` are sometimes used to keep a portion of a web page hidden, and can on occasion will contain sensitive/useful info, i.e. point to a more vulnerable section of the site that's out of use but still accessible, notes the dev left for themselves and forgot to remove about access, etc. 

To find comments, we can pop open the dev tools in our browser, or use `curl` and manually scan the source code, or potentially use something like `grep` to check for patterns, however as of 2022-03-02_Wed, I can't figure out how to make `grep` and `zsh` cooperate with `<!--` as a search string.

[Challenge URL](https://pentesterlab.com/exercises/intro02/scoring)
Key: `beebf5e3-13c3-482f-962a-02e445bc5fa6`

### Intro 03
This is the first challenge where we use pentesterlabs `score` command to mark the challenge complete. To complete the challenge, we need to run `/usr/local/bin/score` with the given UUID as the argument.
In this instance, we need to run 

`/usr/local/bin/score da053225-cf42-470a-b645-1d4ff90ac7c1`

to complete it.

Here we're abusing a command injection vuln.
A program is setup to run `ping` with an ip address as the parameter, but due to insufficient input filtering, we're able to abuse the built in functions of the command line (I'm assuming it's `bash`) to attach a second command and run it with the same privilege as the original `ping` command.

There's many special characters that allow conditional running of commands on a CLI, a few common ones are listed in the exercise;

 - command1 && command2 that will run command2 if command1 succeeds.
 - command1 || command2 that will run command2 if command1 fails.
 - command1 ; command2 that will run command1 then command2.
 - command1 | command2 that will run command1 and send the output of command1 to command2.

For this challenge, we can input `127.0.0.1 & /usr/local/bin/score da053225-cf42-470a-b645-1d4ff90ac7c1` which will be equivalent to `ping 127.0.0.1 & /usr/local/bin/score da053225-cf42-470a-b645-1d4ff90ac7c1`. The `&` operator tells the CLI to run both commands simultaneously. 

[Challenge URL](https://pentesterlab.com/exercises/intro03/scoring)
Score Command: `/usr/local/bin/score da053225-cf42-470a-b645-1d4ff90ac7c1`
