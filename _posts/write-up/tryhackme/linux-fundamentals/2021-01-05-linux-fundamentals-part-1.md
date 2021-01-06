---
title: TryHackMe Writeup - Linux Fundamentals Part 1
date: 2021-01-05 12:45:47 +07:00
tags: [tryhackme]
description: Tryhackme write-up
comments: true
---

# [Linux Fundamentals Part 1](https://tryhackme.com/room/linux1)
---
Get introduced to the Linux basics by learning how to use fundamentally important commands. Put this into practice by deploying and accessing your own remote (web-based) Linux machine.

## [Task 1] Intro
---
Connect to TryHackMe OpenVPN first:
```bash
kali@kali:~$ sudo openvpn path-to/wuvel.ovpn
```

SSH to the machine with username: `shiba1` and password: `shiba1`:
```bash
kali@kali:~$ ssh shiba1@10.10.135.128
```

## [Task 2] Methodology
---

## [Task 3] [Section 2: Running Commands] - Basic Command Execution
---
We can check the detailed information about a command using `man` command:
```bash
shiba1@nootnoot:~$ man echo

ECHO(1)                                        User Commands                                        ECHO(1)

NAME
       echo - display a line of text

SYNOPSIS
       echo [SHORT-OPTION]... [STRING]...
       echo LONG-OPTION

DESCRIPTION
       Echo the STRING(s) to standard output.

       -n     do not output the trailing newline

       -e     enable interpretation of backslash escapes

       -E     disable interpretation of backslash escapes (default)

       --help display this help and exit

       --version
              output version information and exit

       If -e is in effect, the following sequences are recognized:

       \\     backslash

       \a     alert (BEL)
```

Run `echo` command to print something including the newline:
```bash
shiba1@nootnoot:~$ echo hello
hello #this is the output
shiba1@nootnoot:~$
```
## [Task 4] [Section 2: Running Commands] - Manual Pages and Flags
---
We can use `echo` command with tag `-n` to print output without the newline:
```bash
shiba1@nootnoot:~$ echo -n hello
helloshiba1@nootnoot:~$
```

## [Task 5] [Section 3: Basic File Operations] - ls
---
We can use `ls` command to list information about every file / directory in the directory.
```bash
shiba1@nootnoot:~$ ls
shiba1 #this is a file called shiba1
shiba1@nootnoot:~$
```

We can use tag `-a` to list all files / directory including the hidden one (started with `.`):
```bash
shiba1@nootnoot:~$ ls -a
.  ..  .bash_history  .bash_logout  .bashrc  .cache  .gnupg  .local  .profile  shiba1
shiba1@nootnoot:~$ 
```

We can use tag `-l` to list all files / directory in a long list (detailed) format:
```bash
shiba1@nootnoot:~$ ls -l
total 12
-rwsrwxrwx 1 shiba2 shiba2 8432 Feb 13  2020 shiba1 #we can see the permissions, owner, group, etc.
```

## [Task 6] [Section 3: Basic File Operations] - cat
---
We can use `cat` command to outputs contents of files to the terminal:
```bash
shiba1@nootnoot:~$ cat a.txt 
AAA
shiba1@nootnoot:~$
```

We can use tag `-n` o numbers all output lines:
```bash
shiba1@nootnoot:~$ cat -n a.txt 
     1  AAA
shiba1@nootnoot:~$
```

## [Task 7] [Section 3: Basic File Operations] - touch
---
We can use command `touch` to create files:
```bash
shiba1@nootnoot:~$ ls
a.txt  shiba1
shiba1@nootnoot:~$
```

## [Task 8] [Section 3: Basic File Operations] - Running A Binary
---
If we want to run a binary called hello located in directory, we can use `.` shortcut to run it:
```bash
shiba1@nootnoot:~$ ./hello
```

If the file in the home directory, we can use `~` shortcut:
```bash
shiba1@nootnoot:~$ ~/hello
```

If the file in the previous directory, we can use `..` shortcut:
```bash
shiba1@nootnoot:~$ ../hello #go back 1 directory
shiba1@nootnoot:~$ ../../hello #go back 2 directory and so on
```

## [Task 9] Binary - Shiba1
---
To get the shiba2 password, we must create `noot.txt` file and run the `shiba1` binary:
```bash
shiba1@nootnoot:~$ touch noot.txt
shiba1@nootnoot:~$ ./shiba1 
pinguftw
shiba1@nootnoot:~$
```
shiba2 password is **pinguftw**.

## [Task 10] su
---
We can use `su` command to change user:
```bash
su shiba2 #login as shiba2 user
```

We can use `-s` tag to specify which shell used to login:
```bash
su -s bash shiba2 #login as shiba2 with bash shell
```

## [Task 11] Linux Fundamentals 2
---
Room completed! 