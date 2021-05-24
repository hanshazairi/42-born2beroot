# 42cursus - Born2beroot

## Table of Contents
1. [Installation](#installation)
2. [sudo](#sudo)
    - [Step 1: Installing *sudo*](#step-1-installing-sudo)
    - [Step 2: Adding *User* to *sudo* Group](#step-2-adding-user-to-sudo-group)
    - [Step 3: Running *root*-Privileged Commands](#step-3-running-root-privileged-commands)
    - [Step 4: Configuring *sudo*](#step-4-configuring-sudo)
3. [SSH](#ssh)
    - [Step 1: Installing SSH](#step-1-installing-ssh)

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
>ii  sudo <...> Provide limited super user privileges to specific users
>```
Otherwise:
```
$ sudo
-bash: sudo: command not found
```
### Step 1: Installing *sudo*
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
To log all *sudo* commands to `/var/log/sudo`:
```
Defaults        logfile="/var/log/sudo"
```
To enable *TTY*:
```
Defaults        requiretty
```
To set *sudo* paths to `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`:
```
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

## SSH
Check whether the *openssh-server* package is installed on your system via `ssh`.\
\
If *openssh-server* is installed:
```
$ ssh
usage: ssh <...>
<...>
```
>Alternatively, you may check whether *openssh-server* is already installed via `dpkg -l | grep ssh`.
>```
>$ dpkg -l | grep ssh
>ii  openssh-client <...> secure shell (SSH) client, for secure access to remote machines
>ii  openssh-server <...> secure shell (SSH) server, for secure access from remote machines
>ii  openssh-client <...> secure shell (SSH) sftp server module, for SFTP access from remote machines
>```
Otherwise:
```
$ ssh
-bash: ssh: command not found
```

### Step 1: Installing SSH
Install *openssh-server* via `sudo apt install openssh-server`.
```
$ sudo apt install open ssh-server
Reading package lists... Done
<...>
```

### Step 2: Checking SSH Status
Check SSH status via `sudo service ssh status`.
```
$ sudo service ssh status
  ssh.service - OpenBSD Secure Shell Server
   Loaded: <...>
   Active: active (running) <...>
   <...>
```
If status is *inactive*, switch status to *active* via `sudo service ssh start`.
```
$ sudo service ssh start
$ sudo service ssh status
  ssh.service - OpenBSD Secure Shell Server
   Loaded: <...>
   Active: active (running) <...>
   <...>
```
Switch status to *inactive* until next reboot via `sudo service ssh stop`.
```
$ sudo service ssh stop
$ sudo service ssh status
  ssh.service - OpenBSD Secure Shell Server
   Loaded: <...>
   Active: inactive (dead) <...>
   <...>
```
Switch status to *inactive* indefinitely via `sudo systemctl disable ssh`.
```
$ sudo systemctl disable ssh
$ systemctl status ssh
  ssh.service - OpenBSD Secure Shell Server
   Loaded: <...>
   Active: inactive (dead)
     Docs: man:sshd(8)
           man:sshd_config(5)
```
