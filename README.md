# 42cursus - Born2beroot

## Table of Contents
1. [Installation](#installation)
2. [*sudo*](#sudo)
    - [Step 1: Installing *sudo*](#step-1-installing-sudo)
    - [Step 2: Adding *User* to *sudo Group*](#step-2-adding-user-to-sudo-group)
    - [Step 3: Running *root*-Privileged Commands](#step-3-running-root-privileged-commands)
    - [Step 4: Configuring *sudo*](#step-4-configuring-sudo)
3. [SSH](#ssh)
    - [Step 1: Installing SSH](#step-1-installing-ssh)
    - [Step 2: Checking SSH Status](#step-2-checking-ssh-status)
    - [Step 3: Installing UFW](#step-3-installing-ufw)
    - [Step 4: Configuring UFW](#step-4-configuring-ufw)
    - [Step 5: Connecting to Server via SSH *(LAN)*](#step-5-connecting-to-server-via-ssh-lan)
4. [Users](#users)
    - [Step 1: Setting Up A Strong Password Policy](#step-1-setting-up-a-strong-password-policy)
       - [Password Age](#password-age)
       - [Password Strength](#password-strength)
    - [Step 2: Creating A New *User*](#step-2-creating-a-new-user)
    - [Step 3: Creating A New *Group*](#step-3-creating-a-new-group)

## Installation

## *sudo*

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
Verify whether *sudo* was successfully installed via `dpkg -l | grep sudo`.
```
# dpkg -l | grep sudo
ii  sudo <...> Provide limited super user privileges to specific users
```

### Step 2: Adding *User* to *sudo Group*
Add *User* to *sudo Group* via `adduser <username> sudo`.
```
# adduser <username> sudo
```
>Alternatively, add *User* to *sudo Group* via `usermod -aG sudo <username>`.
>```
># usermod -aG sudo <username>
>```
`reboot` for changes to take effect, then log in and run `sudo -v`.
```
# reboot
<--->
Debian GNU/Linux 10 <hostname> tty1

<hostname> login: <username>
Password: 
<--->
$ sudo -v
[sudo] password for <username>:
$
```
>Alternatively, verify whether *User* was successfully added to *sudo Group* via `getent group sudo`.
>```
>$ getent group sudo
>sudo:x:27:<username>
>```

### Step 3: Running *root*-Privileged Commands
From here on out, run *root*-privileged commands via prefix `sudo`.\
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
To log all *sudo* commands to `/var/log/sudo/`:
```
$ (cd /var/log && mkdir sudo)d
<~~~>
Defaults        logfile="/var/log/sudo/<filename>"
<~~~>
```
To require *TTY*:
```
Defaults        requiretty
```
To set *sudo* paths to `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`:
```
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

## SSH

### Step 1: Installing SSH
Install *openssh-server* via `sudo apt install openssh-server`.
```
$ sudo apt install openssh-server
Reading package lists... Done
<...>
```
Verify whether *openssh-server* was successfully installed via `dpkg -l | grep ssh`.
```
$ dpkg -l | grep ssh
ii  openssh-client <...> secure shell (SSH) client, for secure access to remote machines
ii  openssh-server <...> secure shell (SSH) server, for secure access from remote machines
ii  openssh-client <...> secure shell (SSH) sftp server module, for SFTP access from remote machines
```

### Step 2: Checking SSH Status
Check SSH status via `sudo service ssh status`.
```
$ sudo service ssh status
  ssh.service - OpenBSD Secure Shell Server
   <...>
   Active: active (running) <...>
   <...>
```
>Alternatively, check SSH status via `systemctl status ssh`.
>```
>$ systemctl status ssh
>  ssh.service - OpenBSD Secure Shell Server
>   <...>
>   Active: active (running) <...>
>   <...>
>```
If status is *inactive*, switch status to *active* via `sudo service ssh start`.
```
$ sudo service ssh start
$ sudo service ssh status
  ssh.service - OpenBSD Secure Shell Server
   <...>
   Active: active (running) <...>
   <...>
```
Switch status to *inactive* via `sudo service ssh stop`.
```
$ sudo service ssh stop
$ sudo service ssh status
  ssh.service - OpenBSD Secure Shell Server
   <...>
   Active: inactive (dead) <...>
   <...>
```
Disable automatic switch status to *active* upon reboot via `sudo systemctl disable ssh`.
```
$ sudo systemctl disable ssh
Synchronizing <...>
Executing: <...>
$ systemctl status ssh
  ssh.service - OpenBSD Secure Shell Server
   Loaded: (/lib/systemd/system/ssh.service; disabled; <...>
   <...>
```
Enable automatic switch status to *active* upon reboot via `sudo systemctl enable ssh`.
```
$ sudo systemctl enable ssh
Synchronizing <...>
Executing: <...>
Created symlink <...>
<...>
$ systemctl status ssh
  ssh.service - OpenBSD Secure Shell Server
   Loaded: (/lib/systemd/system/ssh.service; enabled; <...>
   <...>
```

### Step 3: Installing UFW
Install *ufw* via `sudo apt install ufw`.
```
$ sudo apt install ufw
Reading package lists... Done
<...>
```
Verify whether *ufw* was successfully installed via `dpkg -l | grep ufw`.
```
$ dpkg -l | grep ufw
ii  ufw <...> program for managing a Netfilter firewall
```

### Step 4: Configuring UFW
To configure *UFW*, edit the file `/etc/ssh/sshd_config` via `sudo vi /etc/ssh/sshd_config`, specifically the below lines:
```
$ sudo vi /etc/ssh/sshd_config
<~~~>
#Port 22
#PermitRootLogin prohibit-password
<~~~>
```
To leave only port 4242 open, replace below line
```
#Port 22
```
with:
```
Port 4242
```
To disable *SSH* login as *root* irregardless of authentication mechanism, replace below line
```
#PermitRootLogin prohibit-password
```
with:
```
PermitRootLogin no
```

### Step 5: Connecting to Server via SSH (LAN)
On your virtual machine, check internal IP address via `hostname -I`.
```shell
$ hostname -I
192.168.56.3
```
On your host machine, connect to your virtual machine via SSH using port 4242 via `ssh <username>@<ip-address> -p 4242`.
```
$ ssh <username>@<ip-address> -p 4242
<username>@<ip-address>'s password:
Linux <...>
<...>
Last login: <...>
$ 
```
Terminate SSH session at any time via `logout`.
```
$ logout
Connection to <ip-address> closed.
```
>Alternatively, terminate SSH session via `exit`.
>```
>$ exit
>logout
>Connection to <ip-address> closed.
>```

## Users

### Step 1: Setting Up A Strong Password Policy

#### Password Age
Firstly, to set up policies in relation to password age, edit the file `/etc/login.defs` via `sudo vi /etc/login.defs`, specifically the below lines:
```
$ sudo vi /etc/login.defs
<~~~>
PASS_MAX_DAYS   99999
PASS_MIN_DAYS   0
PASS_WARN_AGE   7
<~~~>
```
To set password to expire every 30 days, replace below line
```
PASS_MAX_DAYS   99999
```
with:
```
PASS_MAX_DAYS   30
```
To set minimum number of days between password changes to 2 days, replace below line
```
PASS_MIN_DAYS   0
```
with:
```
PASS_MIN_DAYS   2
```
To send *User* a warning message 7 days *(defaults to 7 anyway)* before password expiry, replace below line
```
PASS_WARN_AGE   7
```
with:
```
PASS_WARN_AGE   7
```

#### Password Strength
Secondly, to set up policies in relation to password strength, install the *libpam-pwquality* package.
```
$ sudo apt install libpam-pwquality
Reading package lists... Done
<...>
```
Verify whether *libpam-pwquality* was successfully installed via `dpkg -l | grep libpam-pwquality`.
```
$ dpkg -l | grep libpam-pwquality
ii  libpam-pwquality:amd64 <...> PAM module to check password strength
```
Edit the file `/etc/pam.d/common-password` via `sudo vi /etc/pam.d/common-password`, specifically the below line:
```
$ sudo vi /etc/pam.d/common-password
<~~~>
password        requisite                       pam_pwquality.so retry=3
<~~~>
```
To set password minimum length to 10 characters, add below option to the above line.
```
minlen=10
```
To require password to contain at least an uppercase character and a numeric character:
```
ucredit=-1 dcredit=-1
```
To set a maximum of 3 consecutive identical characters:
```
maxrepeat=3
```
To reject the password if it contains `<username>` in some form:
```
reject_username
```
To set the number of changes required in the new password from the old password to 7:
```
difok=7
```
To implement the same policy on *root*:
```
enforce_for_root
```
Finally, it should look like the below:
```
password        requisite                       pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```

### Step 2: Creating A New User
Create a new *User* via `sudo useradd <username>`.
```
$ sudo useradd <username>
```
Give the newly-created *User* a password via `sudo passwd <username>`.
```
$ sudo passwd <olivia>
New password:
Retype new password:
passwd: password updated successfully
```
Check newly-created *User*'s password expiry information via `sudo chage -l <username>`.
```
$ sudo chage -l <username>
Last password change					: <last-password-change-date>
Password expires					: <last-password-change-date + PASS_MAX_DAYS>
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: <PASS_MIN_DAYS>
Maximum number of days between password change		: <PASS_MAX_DAYS>
Number of days of warning before password expires	: <PASS_WARN_AGE>
```
If you must, delete a *User* via `sudo userdel <username>`.
```
$ sudo userdel <username>
```

### Step 3: Creating A New Group
Create a new *Group* via `sudo groupadd <groupname>`.
```
$ sudo groupadd <groupname>
```
Add *User* to *Group* via `sudo adduser <username> <groupname>`.
```
$ sudo adduser <username> <groupname>
```
>Alternatively, add *User* to *Group* via `sudo usermod -aG <groupname> <username>`.
>```
>$ sudo usermod -aG <groupname> <username>
>```
Verify whether *User* was successfully added to *Group* via `getent group <groupname>`.
```
$ getent group <groupname>
<groupname>:x:1001:<username>
```
If you must, remove a *User* from a *Group* via `sudo deluser <username> <groupname>`.
```
$ sudo deluser <username> <groupname>
```
Again, if you must, delete a *Group* via `sudo groupdel <groupname>`.
```
$ sudo groupdel <groupname>
```
