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
    - [Step 2: Configuring SSH](#step-2-configuring-ssh)
    - [Step 3: Installing UFW](#step-3-installing-ufw)
    - [Step 4: Configuring UFW](#step-4-configuring-ufw)
    - [Step 5: Connecting to Server via SSH](#step-5-connecting-to-server-via-ssh)
4. [Users](#users)
    - [Step 1: Setting Up A Strong Password Policy](#step-1-setting-up-a-strong-password-policy)
       - [Password Age](#password-age)
       - [Password Strength](#password-strength)
    - [Step 2: Creating A New *User*](#step-2-creating-a-new-user)
    - [Step 3: Creating A New *Group*](#step-3-creating-a-new-group)
5. [Bonus](#bonus)
    1. [Linux Lighttpd MariaDB PHP *(LLMP)* Stack](#linux-lighttpd-mariadb-php-llmp-stack)
        - [Step 1: Installing Lighttpd](#step-1-installing-lighttpd)
        - [Step 2: Installing MariaDB](#step-2-installing-mariadb)
        - [Step 3: Installing PHP](#step-3-installing-php)
        - [Step 4: Installing WordPress](#step-4-installing-wordpress)

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
$ sudo apt update
```

### Step 4: Configuring *sudo*
To configure *sudo*, first create a new file in `/etc/sudoers.d/` via `sudo vi /etc/sudoers.d/<filename>`.
```
$ sudo vi /etc/sudoers.d/<filename> #<filename> shall not end in '~' or contain '.'
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

### Step 2: Configuring SSH
To configure SSH, edit the file `/etc/ssh/sshd_config` via `sudo vi /etc/ssh/sshd_config`, specifically the below lines:
```
$ sudo vi /etc/ssh/sshd_config
<~~~>
#Port 22
#PermitRootLogin prohibit-password
<~~~>
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
Enable the Firewall via `sudo ufw enable`.
```
$ sudo ufw enable
Firewall is active and enabled on system startup
```
Allow incoming connections using Port 4242 via `sudo ufw allow 4242`.
```
$ sudo ufw allow 4242
Rule added
Rule added (v6)
```
Check UFW status via `sudo ufw status`.
```
$ sudo ufw status
Status: active
To                          Action      From
--                          ------      ----
4242                        ALLOW       Anywhere
4242 (v6)                   ALLOW       Anywhere (v6)
```

### Step 5: Connecting to Server via SSH
On your virtual machine, check internal IP address via `hostname -I`.
```shell
$ hostname -I
10.0.2.15
```
On another device, connect to your virtual machine via SSH using Port 4242 via `ssh <username>@<ip-address> -p 4242`.
```
$ ssh <username>@<ip-address> -p 4242
<username>@<ip-address>'s password:
Linux <hostname> <...>
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
To send *User* a warning message 7 days *(defaults to 7 anyway)* before password expiry, keep below line as is.
```
PASS_WARN_AGE   7
```
Finally, it should look like the below:
```
PASS_MAX_DAYS   30
PASS_MIN_DAYS   2
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
Create a new *User* via `sudo adduser <username>`.
```
$ sudo adduser <username>
<...>
New password:
Retype new password:
<...>
Is the information correct? [Y/n]
```
Verify whether *User* was successfully created via `getent passwd <username>`.
```
$ getent passwd <username>
<username>:x:<...>:/bin/bash
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

### Step 3: Creating A New Group
Create a new *user42 Group* via `sudo addgroup user42`.
```
$ sudo addgroup user42
Adding group `user42' (GID 1001) ...
Done
```
Add *User* to *user42 Group* via `sudo adduser <username> user42`.
```
$ sudo adduser <username> user42
```
>Alternatively, add *User* to *Group* via `sudo usermod -aG user42 <username>`.
>```
>$ sudo usermod -aG user42 <username>
>```
Verify whether *User* was successfully added to *user42 Group* via `getent group user42`.
```
$ getent group user42
user42:x:1001:<username>
```

## Bonus

### Linux Lighttpd MariaDB PHP *(LLMP)* Stack

#### Step 1: Installing Lighttpd
Install *lighttpd* via `sudo apt install lighttpd`.
```
$ sudo apt install lighttpd
Reading package lists... Done
<...>
```
Verify whether *lighttpd* was successfully installed via `dpkg -l | grep lighttpd`.
```
$ dpkg -l | grep lighttpd
ii  lighttpd <...> fast webserver with minimal memory footprint
ii  lighttpd-modules-ldap <...> LDAP-based modules for lighttpd
ii  lighttpd-modules-mysql <...> MYSQL-based modules for lighttpd
```

#### Step 2: Installing MariaDB
Install *mariadb-server* via `sudo apt install mariadb-server`.
```
$ sudo apt install mariadb-server
Reading package lists... Done
<...>
```
Verify whether *mariadb-server* was successfully installed via `dpkg -l | grep mariadb-server`.
```
$ dpkg -l | grep mariadb-server
ii  libmariadb3:amd64 <...> MariaDB Database client library
ii  mariadb-client-10.3 <...> MariaDB Database client binaries
ii  mariadb-client-core-10.3 <...> MariaDB Database core client binaries
ii  mariadb-common <...> MariaDB common metapackage
ii  mariadb-server <...> MariaDB database server (metapackage depending on the latest version)
ii  mariadb-server-10.3 <...> MariaDB database server binaries
ii  mariadb-server-core-10.3 <...> MariaDB database core server files
```
Start interactive script to remove insecure default settings via `sudo mysql_secure_installation`.
```
$ sudo mysql_secure_installation
<...>
Enter current password for root (enter for none): #Just press enter (do not confuse database root with system root)
<...>
Set root password? [Y/n] n
<...>
Remove anonymous users? [Y/n] Y
<...>
Disallow root login remotely? [Y/n] Y
<...>
Remove test database and access to it? [Y/n] Y
<...>
Reload privilege tables now? [Y/n] Y
<...>
Thanks for using MariaDB!
```
Log in to the MariaDB console via `sudo mariadb`.
```
$ sudo mariadb
Welcome to the MariaDB monitor.  Commands end with ; or \g.
<...>
MariaDB [(none)]>
```
Create a new database via `CREATE DATABASE <database-name>`;.
```
MariaDB [(none)]> CREATE DATABASE <database-name>;
Query OK, 1 row affected (0.000 sec)
```
Create a new *User* and grant them full privileges on the above-created *Database* via `GRANT ALL ON <database-name>.* TO '<username>'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;`.
```
MariaDB [(none)]> GRANT ALL ON <database-name>.* TO '<username>'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
Query OK, 0 rows affected (0.000 sec)
```
Flush the privileges via `FLUSH PRIVILEGES;`.
```
MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.000 sec)
```
Exit the MariaDB shell via `exit`.
```
MariaDB [(none)]> exit
Bye
```
Verify whether *User* was successfully created by logging in to the MariaDB console via `mariadb -u <username> -p`.
```
$ mariadb -u <username> -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
<...>
MariaDB [(none)]>
```
Confirm whether *User* has access to the `<database-name>` database via `SHOW DATABASES;`.
```
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| <database-name>    |
| information_schema |
+--------------------+
2 rows in set (0.000 sec)
```
Exit the MariaDB shell via `exit`.
```
MariaDB [(none)]> exit
Bye
```

#### Step 3: Installing PHP
Install *php-cgi* via `sudo apt install php-cgi php-mysql`.
```
$ sudo apt install php-cgi php-mysql
Reading package lists... Done
<...>
```
Verify whether *php-cgi* was successfully installed via `dpkg -l | grep php`.
```
$ dpkg -l | grep php
ii  php-cgi <...> server-side, HTML-embedded scripting language (CGI binary) (default)
ii  php-common <...> Common files for PHP packages
ii  php-mysql <...> MySQL module for PHP [default]
ii  php7.3-cgi <...> server-side, HTML-embedded scripting language (CGI binary)
ii  php7.3-cli <...> command-line interpreter for the PHP scripting language
ii  php7.3-common <...> documentation, examples and common module for PHP
ii  php7.3-json <...> JSON module for PHP
ii  php7.3-mysql <...> MySQL module for PHP
ii  php7.3-opcache <...> Zend OpCache module for PHP
ii  php7.3-readline <...> readline module for PHP
```
Enable below modules via `sudo lighty-enable-mod fastcgi && sudo lighty-enable-mod fastcgi-php`.
```
$ sudo lighty-enable-mod fastcgi
Enabling fastcgi: ok
Run "service lighttpd force-reload" to enable changes
$ sudo lighty-enable-mod fastcgi-php
Enabling fastcgi-php: ok
Run "service lighttpd force-reload" to enable changes
```

#### Step 4: Installing WordPress
Firstly, to download WordPress, install *wget* via `sudo apt install wget`.
```
$ sudo apt install wget
Reading package lists... Done
<...>
```
Download WordPress to `/var/www/html/` via `cd /var/www/html && wget http://wordpress.org/latest.tar.gz`.
```
$ cd /var/www/html
$ wget http://wordpress.org/latest.tar.gz
<...>
<...> - 'latest.tar.gz' saved <...>
```
Extract the downloaded content via `sudo tar -xzvf latest.tar.gz`.
```
$ sudo tar -xzvf latest.tar.gz
```
Remove tarball via `sudo rm -rf latest.tar.gz`.
```
$ sudo rm -rf latest.tar.gz
```
Create WordPress configuration file from its sample via `sudo cp wp-config-sample.php wp-config.php`.
```
$ sudo cp wp-config-sample.php wp-config.php
```
To configure WordPress to reference the MariaDB database & user created earlier, edit the file `wp-config.php` via `sudo vi wp-config.php`, specifically the below lines:
```
$ sudo vi wp-config.php
<~~~>
define( 'DB_NAME', 'database_name_here' );^M
define( 'DB_USER', 'username_here' );^M
define( 'DB_PASSWORD', 'password_here' );^M
<~~~>
```
Replace the above with:
```
define( 'DB_NAME', '<database-name>' );^M
define( 'DB_USER', '<username>' );^M
define( 'DB_PASSWORD', '<password>' );^M
```
