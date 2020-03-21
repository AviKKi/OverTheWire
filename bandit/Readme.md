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