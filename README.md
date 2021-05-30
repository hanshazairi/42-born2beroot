# 42cursus - Born2beroot

## Table of Contents
1. [Installation](#installation)
2. [*sudo*](#sudo)
    - [Step 1: Installing *sudo*](#step-1-installing-sudo)
    - [Step 2: Adding User to *sudo* Group](#step-2-adding-user-to-sudo-group)
    - [Step 3: Running *root*-Privileged Commands](#step-3-running-root-privileged-commands)
    - [Step 4: Configuring *sudo*](#step-4-configuring-sudo)
3. [SSH](#ssh)
    - [Step 1: Installing & Configuring SSH](#step-1-installing--configuring-ssh)
    - [Step 2: Installing & Configuring UFW](#step-2-installing--configuring-ufw)
    - [Step 3: Connecting to Server via SSH](#step-3-connecting-to-server-via-ssh)
4. [User Management](#user-management)
    - [Step 1: Setting Up a Strong Password Policy](#step-1-setting-up-a-strong-password-policy)
       - [Password Age](#password-age)
       - [Password Strength](#password-strength)
    - [Step 2: Creating a New User](#step-2-creating-a-new-user)
    - [Step 3: Creating a New Group](#step-3-creating-a-new-group)
5. [Bonus](#bonus)
    - [Linux Lighttpd MariaDB PHP *(LLMP)* Stack](#1-linux-lighttpd-mariadb-php-llmp-stack)
       - [Step 1: Installing Lighttpd](#step-1-installing-lighttpd)
       - [Step 2: Installing & Configuring MariaDB](#step-2-installing--configuring-mariadb)
       - [Step 3: Installing PHP](#step-3-installing-php)
       - [Step 4: Downloading & Configuring WordPress](#step-4-downloading--configuring-wordpress)
       - [Step 5: Configuring Lighttpd](#step-5-configuring-lighttpd)
    - [File Transfer Protocol *(FTP)*](#2-file-transfer-protocol-ftp)
       - [Step 1: Installing & Configuring *vsftpd*](#step-1-installing--configuring-vsftpd)

## Installation
At the time of writing, the latest stable version of [Debian](https://www.debian.org) is *Debian 10 Buster*. Watch my *bonus* installation walkthrough *(no audio)* [here](https://youtu.be/2w-2MX5QrQw).

## *sudo*

### Step 1: Installing *sudo*
Switch to *root* and its environment via `su -`.
```
$ su -
Password:
#
```
Install *sudo* via `apt install sudo`.
```
# apt install sudo
```
Verify whether *sudo* was successfully installed via `dpkg -l | grep sudo`.
```
# dpkg -l | grep sudo
```

### Step 2: Adding User to *sudo* Group
Add user to *sudo* group via `adduser <username> sudo`.
```
# adduser <username> sudo
```
>Alternatively, add user to *sudo* group via `usermod -aG sudo <username>`.
>```
># usermod -aG sudo <username>
>```
`reboot` for changes to take effect, then log in and verify via `sudo -v`.
```
# reboot
<--->
Debian GNU/Linux 10 <hostname> tty1

<hostname> login: <username>
Password: <password>
<--->
$ sudo -v
[sudo] password for <username>: <password>
```
>Alternatively, verify whether user was successfully added to *sudo* group via `getent group sudo`.
>```
>$ getent group sudo
>sudo:x:27:<username>
>```

### Step 3: Running *root*-Privileged Commands
From here on out, run *root*-privileged commands via prefix `sudo`. For instance:
```
$ sudo apt update
```

### Step 4: Configuring *sudo*
Configure *sudo* via `sudo vi /etc/sudoers.d/<filename>`. `<filename>` shall not end in `~` or contain `.`.
```
$ sudo vi /etc/sudoers.d/<filename>
```
To limit authentication using *sudo* to 3 attempts *(defaults to 3 anyway)* in the event of an incorrect password, add below line to the file.
```
Defaults        passwd_tries=3
```
To add a custom error message in the event of an incorrect password:
```
Defaults        badpass_message="<custom-error-message>"
```
###
To log all *sudo* commands to `/var/log/sudo/<filename>`:
```
$ sudo mkdir /var/log/sudo
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
Finally, it should look like the below:
```
Defaults        passwd_tries=3
Defaults        badpass_message="<custom-error-message>"
Defaults        logfile="/var/log/sudo/<filename>"
Defaults        requiretty
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

## SSH

### Step 1: Installing & Configuring SSH
Install *openssh-server* via `sudo apt install openssh-server`.
```
$ sudo apt install openssh-server
```
Verify whether *openssh-server* was successfully installed via `dpkg -l | grep ssh`.
```
$ dpkg -l | grep ssh
```
Configure SSH via `sudo vi /etc/ssh/sshd_config`.
```
$ sudo vi /etc/ssh/sshd_config
```
To set up SSH using Port 4242, replace below line:
```
#Port 22
```
with:
```
Port 4242
```
To disable SSH login as *root* irregardless of authentication mechanism, replace below line
```
#PermitRootLogin prohibit-password
```
with:
```
PermitRootLogin no
```
Check SSH status via `sudo service ssh status`.
```
$ sudo service ssh status
```
>Alternatively, check SSH status via `systemctl status ssh`.
>```
>$ systemctl status ssh
>```

### Step 2: Installing & Configuring UFW
Install *ufw* via `sudo apt install ufw`.
```
$ sudo apt install ufw
```
Verify whether *ufw* was successfully installed via `dpkg -l | grep ufw`.
```
$ dpkg -l | grep ufw
```
Enable the Firewall via `sudo ufw enable`.
```
$ sudo ufw enable
```
Allow incoming connections using Port 4242 via `sudo ufw allow 4242`.
```
$ sudo ufw allow 4242
```
Check UFW status via `sudo ufw status`.
```
$ sudo ufw status
```

### Step 3: Connecting to Server via SSH
On your virtual machine, check internal IP address via `hostname -I`.
```
$ hostname -I
<ip-address>
```
On another device, SSH into your virtual machine using Port 4242 via `ssh <username>@<ip-address> -p 4242`.
```
$ ssh <username>@<ip-address> -p 4242
<username>@<ip-address>'s password: <password>
```
Terminate SSH session at any time via `logout`.
```
$ logout
```
>Alternatively, terminate SSH session via `exit`.
>```
>$ exit
>logout
>```

## User Management

### Step 1: Setting Up a Strong Password Policy

#### Password Age
Firstly, set up policies in relation to password age via `sudo vi /etc/login.defs`.
```
$ sudo vi /etc/login.defs
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
To send *User* a warning message 7 days *(defaults to 7 anyway)* before password expiry, keep below line as is.
```
PASS_WARN_AGE   7
```

#### Password Strength
Secondly, to set up policies in relation to password strength, install the *libpam-pwquality* package.
```
$ sudo apt install libpam-pwquality
```
Verify whether *libpam-pwquality* was successfully installed via `dpkg -l | grep libpam-pwquality`.
```
$ dpkg -l | grep libpam-pwquality
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

### Step 2: Creating a New User
Create a new user via `sudo adduser <username>`.
```
$ sudo adduser <username>
```
Verify whether user was successfully created via `getent passwd <username>`.
```
$ getent passwd <username>
<username>:x:<...>:/bin/bash
```
Check newly-created user's password expiry information via `sudo chage -l <username>`.
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

### Step 3: Creating a New Group
Create a new *user42* group via `sudo addgroup user42`.
```
$ sudo addgroup user42
```
Add user to *user42* group via `sudo adduser <username> user42`.
```
$ sudo adduser <username> user42
```
>Alternatively, add user to *user42* group via `sudo usermod -aG user42 <username>`.
>```
>$ sudo usermod -aG user42 <username>
>```
Verify whether user was successfully added to *user42* group via `getent group user42`.
```
$ getent group user42
user42:x:1001:<username>
```

## Bonus

### #1: Linux Lighttpd MariaDB PHP *(LLMP)* Stack

#### Step 1: Installing Lighttpd
Install *lighttpd* via `sudo apt install lighttpd`.
```
$ sudo apt install lighttpd
```
Verify whether *lighttpd* was successfully installed via `dpkg -l | grep lighttpd`.
```
$ dpkg -l | grep lighttpd
```
Allow incoming connections using Port 80 via `sudo ufw allow 80`.
```
$ sudo ufw allow 80
```

#### Step 2: Installing & Configuring MariaDB
Install *mariadb-server* via `sudo apt install mariadb-server`.
```
$ sudo apt install mariadb-server
```
Verify whether *mariadb-server* was successfully installed via `dpkg -l | grep mariadb-server`.
```
$ dpkg -l | grep mariadb-server
```
Start interactive script to remove insecure default settings via `sudo mysql_secure_installation`.
```
$ sudo mysql_secure_installation
Enter current password for root (enter for none): #Just press Enter (do not confuse database root with system root)
Set root password? [Y/n] n
Remove anonymous users? [Y/n] Y
Disallow root login remotely? [Y/n] Y
Remove test database and access to it? [Y/n] Y
Reload privilege tables now? [Y/n] Y
```
Log in to the MariaDB console via `sudo mariadb`.
```
$ sudo mariadb
MariaDB [(none)]>
```
Create a new database via `CREATE DATABASE <database-name>;`.
```
MariaDB [(none)]> CREATE DATABASE <database-name>;
```
Create a new database user and grant them full privileges on the newly-created database via `GRANT ALL ON <database-name>.* TO '<username-2>'@'localhost' IDENTIFIED BY '<password-2>' WITH GRANT OPTION;`.
```
MariaDB [(none)]> GRANT ALL ON <database-name>.* TO '<username-2>'@'localhost' IDENTIFIED BY '<password-2>' WITH GRANT OPTION;
```
Flush the privileges via `FLUSH PRIVILEGES;`.
```
MariaDB [(none)]> FLUSH PRIVILEGES;
```
Exit the MariaDB shell via `exit`.
```
MariaDB [(none)]> exit
```
Verify whether database user was successfully created by logging in to the MariaDB console via `mariadb -u <username-2> -p`.
```
$ mariadb -u <username-2> -p
Enter password: <password-2>
MariaDB [(none)]>
```
Confirm whether database user has access to the database via `SHOW DATABASES;`.
```
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| <database-name>    |
| information_schema |
+--------------------+
```
Exit the MariaDB shell via `exit`.
```
MariaDB [(none)]> exit
```

#### Step 3: Installing PHP
Install *php-cgi* & *php-mysql* via `sudo apt install php-cgi php-mysql`.
```
$ sudo apt install php-cgi php-mysql
```
Verify whether *php-cgi* & *php-mysql* was successfully installed via `dpkg -l | grep php`.
```
$ dpkg -l | grep php
```

#### Step 4: Downloading & Configuring WordPress
Install *wget* via `sudo apt install wget`.
```
$ sudo apt install wget
```
Download WordPress to `/var/www/html` via `sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html`.
```
$ sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html
```
Extract downloaded content via `sudo tar -xzvf /var/www/html/latest.tar.gz`.
```
$ sudo tar -xzvf /var/www/html/latest.tar.gz
```
Remove tarball via `sudo rm /var/www/html/latest.tar.gz`.
```
$ sudo rm /var/www/html/latest.tar.gz
```
Copy content of `/var/www/html/wordpress` to `/var/www/html` via `sudo cp -r /var/www/html/wordpress/* /var/www/html`.
```
$ sudo cp -r /var/www/html/wordpress/* /var/www/html
```
Remove `/var/www/html/wordpress` via `sudo rm -rf /var/www/html/wordpress`
```
$ sudo rm -rf /var/www/html/wordpress
```
Create WordPress configuration file from its sample via `sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php`.
```
$ sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
```
Configure WordPress to reference previously-created MariaDB database & user via `sudo vi /var/www/html/wp-config.php`.
```
$ sudo vi /var/www/html/wp-config.php
```
Replace the below
```
define( 'DB_NAME', 'database_name_here' );^M
define( 'DB_USER', 'username_here' );^M
define( 'DB_PASSWORD', 'password_here' );^M
```
with:
```
define( 'DB_NAME', '<database-name>' );^M
define( 'DB_USER', '<username-2>' );^M
define( 'DB_PASSWORD', '<password-2>' );^M
```

#### Step 5: Configuring Lighttpd
Enable below modules via `sudo lighty-enable-mod fastcgi; sudo lighty-enable-mod fastcgi-php; sudo service lighttpd force-reload`.
```
$ sudo lighty-enable-mod fastcgi
$ sudo lighty-enable-mod fastcgi-php
$ sudo service lighttpd force-reload
```

### #2: File Transfer Protocol *(FTP)*

#### Step 1: Installing & Configuring *vsftpd*
Install FTP via `sudo apt install vsftpd`.
```
$ sudo apt install vsftpd
```
Verify whether *vsftpd* was successfully installed via `dpkg -l | grep vsftpd`.
```
$ dpkg -l | grep vsftpd
```
Allow incoming connections using Port 21 via `sudo ufw allow 21`.
```
$ sudo ufw allow 21
```
Configure *vsftpd* via `sudo vi /etc/vsftpd.conf`.
```
$ sudo vi /etc/vsftpd.conf
```
To enable any form of FTP write command, uncomment below line:
```
31 #write_enable=YES
```
To set root folder for FTP-connected user to `/home/<username>/ftp`, add below line:
```
$ sudo mkdir /home/<username>/ftp
$ sudo mkdir /home/<username>/ftp/files
$ sudo chown nobody:nogroup /home/<username>/ftp
$ sudo chmod a-w /home/<username>/ftp
<~~~>
user_sub_token=$USER
local_root=/home/$USER/ftp
<~~~>
```
To prevent user from accessing files or using commands outside the directory tree, uncomment below line:
```
114 #chroot_local_user=YES
```
To whitelist FTP, add below lines:
```
$ sudo vi /etc/vsftpd.userlist
$ echo <username> | sudo tee -a /etc/vsftpd.userlist
<~~~>
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
<~~~>
```
