# 42cursus - Born2beroot

## Table of Contents
1. [Installation](#installation)
2. [sudo](#sudo)
    - [Step 1: Installation](#step-1-installation)
    - [Step 2: Adding *User* to *sudo* Group](#step-2-adding-user-to-sudo-group)
    - [Step 3: Running *root*-Privileged Commands](#step-3-running-root-privileged-commands)
    - [Step 4: Configuring *sudo*](#step-4-configuring-sudo)

## Installation

## *sudo*
Check whether the *sudo* package is installed on your system via `sudo`.\
\
If *sudo* is installed:
```
$ sudo
usage: sudo -h | -K | -k | -V
<...>
```
>Alternatively, you may check whether *sudo* is already installed via `dpkg -l | grep sudo`.
>```
>$ dpkg -l | grep sudo
>ii sudo <...>
>super user privileges to specific users
>```
Otherwise:
```
$ sudo
-bash: sudo: command not found
```
### Step 1: Installation
Switch to the *root* user and its environment via `su -`.
```
$ su -
Password:
#
```
Install *sudo* via `apt install sudo`.
```
# apt install sudo
Reading package lists... Done
<...>
Processing triggers for systemd (241-7~deb10u7) ...
```

### Step 2: Adding *User* to *sudo* Group
Add *User* to *sudo* group via `usermod -aG sudo <username>`.
```
# usermod -aG sudo <username>
```
>Alternatively, you may add *User* to *sudo* group via `adduser <username> sudo`.
>```
># adduser <username> sudo
>```
Switch back to *User* from *root* via `logout` and verify whether *User* has been added to *sudo* group via `sudo -v`.
```
# logout
$ sudo -v
Password:
```
>Alternatively, you may verify whether User has been added to *sudo* group via `getent group sudo`.
>```
>$ getent group sudo
>sudo:x:27:<username>
>```
If the above fails, logout from *User* via `logout`, log back in, then run `sudo -v` again.
```
# logout
$ sudo -v
Sorry, user <username> may not run sudo on <hostname>.
$ logout
<--->
Debian GNU/Linux 10 <hostname> tty1

<hostname> login: <username>
Password: 
<--->
$ sudo -v
[sudo] password for <username>:
$
```

### Step 3: Running *root*-Privileged Commands
You may now run *root*-privileged commands via prefix `sudo`.\
\
For instance:
```
$ sudo apt-get update
```

### Step 4: Configuring *sudo*
To configure *sudo*, change directory into `/etc/sudoers.d/` and create a file via `cd /etc/sudoers.d/ && sudo touch <filename>`.
```
$ cd /etc/sudoers.d
$ sudo touch <filename> #<filename> shall not end in '~' or contain '.'
```
To limit authentication using *sudo* to 3 attempts *(defaults to 3 anyway)* in the event of an incorrect password, add below line to the previously created file via `sudo vi <filename>`:
```
$ sudo vi <filename>
<~~~>
Defaults        passwd_tries=3
<~~~>
```
To add a custom error message in the event of an incorrect password:
```
Defaults        badpass_message="<custom-error-message>"
```
###
To log all *sudo* commands:
```
Defaults        logfile="/var/log/sudo"
```
To enable *TTY*:
```
Defaults        requiretty
```
To set *sudo* paths:
```
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```
