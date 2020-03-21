### Notes for each level
Below are the notes I have made to keep track of my progress, please don't cheat if you want to learn something.


#### Level 0
Simple ssh login
`$ssh bandit0@bandit.labs.overthewire.org -p 2220`
password in bandit0, and password is in readme
`$cat readme`

#### Level 1
Simply use cat command
`$cat /home/bandit1/-`

#### Level 2
Again special type of file name
`$cat spaces\ in\ this\ filename`

#### Level 3
Simply using hidden files
`$ls`
`$cd inhere`
`$ls`
`$ls .*`
`$cat .hidden`

#### Level 4
Ok this one really got, filename starting witha a dash '-file00' made it quite hard but python was there to rescue, TODO: look for more options
`$cd inhere`
`$python -c "print [open('-file0'+str(i)).read() for i in range(10)]"`

#### Level 5
Quite tricky huh.. too many files ;;_;;
I should try reading instructions
File with password has following properties
- human-readable
- 1033 bytes in size
- not executable
`$find . -type f -size 1033c -exec cat {} \;`
Just the size was enough to pinout the file :/

#### Level 6
LOL no files here 0_0"
Again going to instruction
file has following properties
- owned by user bandit7
- owned by group bandit6
- 33 bytes in size
`$find / -size 33c -user bandit7`
mostly everything has permission denied except /var/lib/dpkg/info/bandit7.password
`$cat /var/lib/dpkg/info/bandit7.password`
viola!! got the pass

#### Level 7
New Rule: First read instruction
Why don't they mention it in ssh, it would be much easier that way
This was easier
`$cat data.txt | grep millionth`

#### Level 8
This is getting tensed so I'll just use python :P
`$python`
`>>>from collections import Counter`
`>>>Counter(open("data.txt").read().split('\n')[:-1])`
Every thing has a count of 10 except our password

#### Level 9
This was pretty straight forward
`$strings data.txt | grep "^="`

#### Level 10
No more instruction :P
Password is simply base64 ecoded
`$python -c 'import base64;print base64.b64decode(open("data.txt").read())'`

#### Level 11
Again looks simple, probably mono alphabetical substitution
`$cat data.txt`
Even easier it turned out to be ROT13 (it was clearly mentioned in instructions saw later, so follow the rules XD)

#### Level 12
This looks quite complex, a hex dump huh... first we'll do a reverse hex dump
BZh91AY in the hex dump looks like a magic number googling revealed it's bz2
```sh
bandit12@bandit:~$ ls
data.txt
bandit12@bandit:~$ mkdir /tmp/avikki
bandit12@bandit:~$ cp data.txt /tmp/avikki
bandit12@bandit:~$ cd /tmp/avikki
bandit12@bandit:/tmp/avikki$ xxd -r data.txt > data.bin
bandit12@bandit:/tmp/avikki$ python3
>>> import bz2
>>> bz2.decompress(open('data.bz2','rb').read()[25:])
```
I dumped that file to my local machine and used binwalk to find out it's 'gzip compressed data'
```sh
avikki@avikki:~/lox$ zcat data4.bin > data5.bin 
avikki@avikki:~/lox$ binwalk data5.bin

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             POSIX tar archive (GNU), owner user name: "n"
 
avikki@avikki:~/lox$ tar -xf data5.bin 
avikki@avikki:~/lox$ binwalk data6.bin

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             bzip2 compressed data, block size = 900k

avikki@avikki:~/lox$ bzip2 -d data6.bin
bzip2: Cant guess original name for data6.bin -- using data6.bin.out

avikki@avikki:~/lox$ bzip2 -d data6.bin

avikki@avikki:~/lox$ binwalk data6.bin.out

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             POSIX tar archive (GNU), owner user name: "n"
```

After more such inception level of compression I got my password in data9.bin
TODO: look for other's solutions

#### Level 13
Simple one 
`$ssh -i sshkey.private bandit14@localhost`
`$ cat /etc/bandit_pass/bandit14` # this is used in next level

#### Level 14
Key is retrieved using simple telnet program running at 3000 and pass from last level
```s
    $ telnet localhost 3000
    Trying 127.0.0.1...
    Connected to localhost.
    Escape character is '^]'.
    4wcYUJFw0k0XLShlDzztnxxxxxxxxxx
    Correct!
    BfMYroe26WYalil77FoDxxxxxxxxxxxx

    Connection closed by foreign host.
```

#### Level 15
Finally we are using some secure communication, same as previous challenge but with an encypted channel

`bandit15@bandit:~$ openssl s_client -ign_eof -connect localhost:30001`

#### Level 16
Simple port scanning and doing as last level to get the pass
```sh
bandit16@bandit:~$ telnet localhost 31790
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
cluFn7wTiGryunymYOu4RcffSxQluehd
Connection closed by foreign host.
bandit16@bandit:~$ openssl s_client -ign_eof -connect localhost:31790
```

### Level 17
This one was pretty self explanatory, just need to compare two huge files
```s
bandit17@bandit:~$ diff passwords.old passwords.new 
42c42
< hlbSBPAWJmL6WFDb06gpTx1pPButblOA
---
> kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
```

### Level 18
Goodbye as soon as I login, nice trick
```s
$ ssh -t bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password: 
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
Connection to bandit.labs.overthewire.org closed.
```

#### Level 19
TODO: understand how this binary works
`s
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55xxxxxxxxxxxxxxx
`

#### Level 20
`s
bandit20@bandit:~$ echo "GbKksEFF4yrVs6il55v6gwY5aVje5f0j" | nc -l localhost -p 3000 &
[2] 18861
bandit20@bandit:~$ ./suconnect 3000
Read: GbKksEFF4yrVs6il55v6gwY5aVje5f0j
Password matches, sending next password
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
`

#### Level 21
I would call this one `a little poking around`
```s
bandit21@bandit:~$ cd /etc/cron.d/

bandit21@bandit:/etc/cron.d$ ls
atop  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24

bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null

bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI

```

#### Level 22
I was expecting obsfruscated code but it was much easier
```s
bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget

bandit22@bandit:~$ export myname="bandit23"

bandit22@bandit:~$ echo I am user $myname | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349

bandit22@bandit:~$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8xxxxxxxxxx
```

#### Level 23

```s
bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
	echo "Handling $i"
	timeout -s 9 60 ./$i
	rm -f ./$i
    fi
done

bandit23@bandit:~$ mkdir /tmp/avikki24
bandit23@bandit:~$ cd /tmp/avikki24
bandit23@bandit:/tmp/avikki24$ nano myscript.sh
bandit23@bandit:/tmp/avikki24$ chmod +x myscript.sh 
bandit23@bandit:/tmp/avikki24$ cp myscript.sh  /var/spool/bandit24/
bandit23@bandit:/tmp/avikki24$ ls -al /var/spool/bandit24/myscript.sh
-rwxr-xr-x 1 bandit23 bandit23 61 Mar 21 13:34 /var/spool/bandit24/myscript.sh
bandit23@bandit:/tmp/avikki24$ cat /tmp/bandit24_pass
UoMYTrfrBFHyQXxxxxxxxxxxxxxxxx
```

inside myscript.sh
```s
#!/bin/bash

cp /etc/bandit_pass/bandit24 /tmp/bandit24_pass
```

#### Level 24
A simple brute force program 

```s
bandit24@bandit:~$ mkdir /tmp/avikki25
bandit24@bandit:~$ cd /tmp/avikki25
bandit24@bandit:/tmp/avikki25$ python3 -c "open('data.txt','w').write('\n'.join(['UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ '+str(i).zfill(4) for i in range(0,10000)]))" 
bandit24@bandit:/tmp/avikki25$ cat data.txt | nc localhost 30002 | grep -v "^W"
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Timeout. Exiting.
bandit24@bandit:/tmp/avikki25$ split -l5000 data.txt 
bandit24@bandit:/tmp/avikki25$ cat xaa | nc localhost 30002 | grep -v "Wrong"
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Timeout. Exiting.
bandit24@bandit:/tmp/avikki25$ cat xab | nc localhost 30002 | grep -v "Wrong"
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Correct!
The password of user bandit25 is uNG9O58gUE7snukf3xxxxxxxxxxx

Exiting.
```

#### Level 25
It required me to resize the window and press `v` to enter vim editor then i edited /etc/bandit_pass/bandit26 to look at the password. Had to google the breaking of more command, startup command can be checked by `$cat /etc/passwd | grep bandit26`
```s
bandit25@bandit:~$ ls
bandit26.sshkey
bandit25@bandit:~$ ssh -i bandit26.sshkey bandit26@localhost
```

#### Level 26

Already inside level 26 with vim access don't know what to do now :/
doing `:set shell=/bin/bash` and `:shell` finally worked out, I'm out on shell on the way to 27

```s
bandit26@bandit:~$ ls
bandit27-do  text.txt
bandit26@bandit:~$ cat text.txt 
  _                     _ _ _   ___   __  
 | |                   | (_) | |__ \ / /  
 | |__   __ _ _ __   __| |_| |_   ) / /_  
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \ 
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/ 
bandit26@bandit:~$ ./bandit27-do echo "grapes"
grapes
bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
3ba3118a22e93127a4edxxxxxxxxxx
```

#### Level 27
So I have to clone a repo now, this one was pretty basic

```s
bandit27@bandit:~$ mkdir /tmp/avikki27
bandit27@bandit:~$ cd /tmp/avikki27
bandit27@bandit:/tmp/avikki27$ git clone ssh://bandit27-git@localhost/home/bandit27-git/repo
Cloning into 'repo'...
Could not create directory '/home/bandit27/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:98UL0ZWr85496EtCRkKlo20X3OPnyPSB5tB5RPbhczc.
Are you sure you want to continue connecting (yes/no)? yes
Failed to add the host to the list of known hosts (/home/bandit27/.ssh/known_hosts).
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit27-git@localhost's password: 
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (3/3), done.
bandit27@bandit:/tmp/avikki27$ ls
repo
bandit27@bandit:/tmp/avikki27$ cd repo/
bandit27@bandit:/tmp/avikki27/repo$ ls
README
bandit27@bandit:/tmp/avikki27/repo$ cat README 
The password to the next level is: 0ef186ac70e04ea33b4c18xxxxxxxxxxx
```

#### Level 28
Again a repo, using commits to hide the password, I guess next will be branches

```s
bandit28@bandit:~$ mkdir /tmp/avikki28
bandit28@bandit:~$ cd /tmp/avikki28
bandit28@bandit:/tmp/avikki28$ git clone ssh://bandit28-git@localhost/home/bandit28-git/repo
Cloning into 'repo'...
Could not create directory '/home/bandit28/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:98UL0ZWr85496EtCRkKlo20X3OPnyPSB5tB5RPbhczc.
Are you sure you want to continue connecting (yes/no)? yes
Failed to add the host to the list of known hosts (/home/bandit28/.ssh/known_hosts).
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit28-git@localhost's password: 
remote: Counting objects: 9, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 2), reused 0 (delta 0)
Receiving objects: 100% (9/9), done.
Resolving deltas: 100% (2/2), done.
bandit28@bandit:/tmp/avikki28$ cd repo/
bandit28@bandit:/tmp/avikki28/repo$ ls
README.md
bandit28@bandit:/tmp/avikki28/repo$ cat README.md 
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx

bandit28@bandit:/tmp/avikki28/repo$ git log --oneline
073c27c fix info leak
186a103 add missing data
b67405d initial commit of README.md
bandit28@bandit:/tmp/avikki28/repo$ git checkout HEAD~1
Note: checking out 'HEAD~1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 186a103... add missing data
bandit28@bandit:/tmp/avikki28/repo$ cat README.md 
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: bbc96594b4e001778eee99xxxxxxxx
```

#### Level 29
This seems like a nice way to learn git, but I already know it :P

```s
bandit29@bandit:~$ mkdir /tmp/avikki29
bandit29@bandit:~$ cd /tmp/avikki29
bandit29@bandit:/tmp/avikki29$ git clone ssh://bandit29-git@localhost/home/bandit29-git/repo
Cloning into 'repo'...
Could not create directory '/home/bandit29/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:98UL0ZWr85496EtCRkKlo20X3OPnyPSB5tB5RPbhczc.
Are you sure you want to continue connecting (yes/no)? yes
Failed to add the host to the list of known hosts (/home/bandit29/.ssh/known_hosts).
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit29-git@localhost's password: 
remote: Counting objects: 16, done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 16 (delta 2), reused 0 (delta 0)
Receiving objects: 100% (16/16), done.
Resolving deltas: 100% (2/2), done.

bandit29@bandit:/tmp/avikki29$ cd repo/
bandit29@bandit:/tmp/avikki29/repo$ ls
README.md
bandit29@bandit:/tmp/avikki29/repo$ cat README.md 
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>

bandit29@bandit:/tmp/avikki29/repo$ cat .*
cat: .: Is a directory
cat: ..: Is a directory
cat: .git: Is a directory
bandit29@bandit:/tmp/avikki29/repo$ ls .*
.:
README.md

..:
repo

.git:
branches  description  hooks  info  objects      refs
config    HEAD         index  logs  packed-refs
bandit29@bandit:/tmp/avikki29/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
bandit29@bandit:/tmp/avikki29/repo$ git checkout dev
Branch dev set up to track remote branch dev from origin.
Switched to a new branch 'dev'
bandit29@bandit:/tmp/avikki29/repo$ cat README.md 
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: 5b90576bedb2cc04c86a9e924cxxxxxx

bandit29@bandit:/tmp/avikki29/repo$ 

```

#### Level 30
Ok I didn't knew this one took a lot of hanging around and arrows in the dark
It turns out git has a feature called tag.
```s
bandit30@bandit:/tmp/avikki30/repo$ git tag
secret
bandit30@bandit:/tmp/avikki30/repo$ git show secret 
47e603bb428404d265f59c4292xxxxxx
```


#### Level 31

This one was simpler just a bit tricky, `*.txt` was added in gitignore, so even if you added a txt forcefully it the git hook at backend would ignore your txt file.

so in first commit remove `.gitignore`, then add `key.txt`
then push it to get the password


