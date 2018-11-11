# Debian 9.0 installation guide

## 1. What

Installation guide for Debian 9 with standard Virtualmin setup

### Revision history

| Author          | Change            | Time       |
|-----------------|-------------------|------------|
| Daniel Leppänen | Minor updates     | 2018-11-11 |
| Daniel Leppänen | First draft       | 2018-09-05 |

## 2. Steps

### 2.1. Initial system upgrade and apt sources

1. Install transport and certificate packages:

    `apt install apt-transport-https ca-certificates`

2. Edit **/etc/apt/sources.list** for main packages only:

    ```sh
        # main packages
        deb http://http.debian.net/debian stretch main contrib #non-free
        deb-src http://http.debian.net/debian stretch main contrib #non-free
        deb http://http.debian.net/debian stretch-updates main contrib #non-free
        deb-src http://http.debian.net/debian stretch-updates main contrib #non-free
        deb http://security.debian.org/ stretch/updates main contrib #non-free
        deb-src http://security.debian.org/ stretch/updates main contrib #non-free
    ```

    > **File:** /etc/apt/sources.list

3. Update apt soureces:
    `apt update`

4. Upgrade system:
    `apt dist-upgrade`

5. Upgrade packages:
    `apt upgrade`

### 2.2. Install packages

#### 2.2.1. Standard packages

`apt install sudo htop locate nmap screen git curl clamav clamav-daemon rkhunter ncdu xz-utils apache2 mariadb-server build-essential`

`sudo apt autoremove`

#### 2.2.2. Docker

> Only if system required docker

See up-to-date installation structions here
https://docs.docker.com/install/linux/docker-ce/debian/


#### 2.2.3. PHP 7.2 (or later)

Available through ondrej/php PPA.
https://launchpad.net/~ondrej/+archive/ubuntu/php

*Run one line at a time:*

```sh
    sudo apt install apt-transport-https lsb-release ca-certificates
    sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
    sudo sh -c 'echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
    sudo apt update

    sudo apt install php7.2-fpm
```

##### PHP Extensions

Some common extensions not installed by default:

* php7.2-bz2
* php7.2-zip
* php-imagick
* php7.2-curl
* php7.2-sqlite3

Install by

`apt install php7.2-bz2 php7.2-zip php-imagick php7.2-curl php7.2-sqlite3`

##### Composer package

By instructions from https://getcomposer.org/download/

*Run in shell:*

```sh
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"

    mv composer.phar /usr/local/bin/composer
```

#### 2.2.4. Nodejs, npm and Yarn

##### Node and npm

*Run in shell:*

```sh
    wget https://nodejs.org/dist/v10.13.0/node-v10.13.0-linux-x64.tar.xz
    tar -xf node-v8.11.1-linux-x64.tar.xz
    cd node-v8.11.1-linux-x64
    cp -R ./* /usr/local/
```

*Verify*:

`npm -v`
`node -v`

##### Yarn

*Run in shell:*

```sh
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

    sudo apt-get update
    sudo apt-get install yarn
```

### 2.3. Users and groups

#### 2.3.1. Admin users

1. Update sshd config

    ```sh
        PermitRootLogin no
        PasswordAuthentication no
    ```

    > **File:**: /etc/ssh/sshd_config

2. Restart sshd `service sshd restart`

3. Edit sudoers `sudo visudo`, add the following line to the group section:

    ```sh
        %admins ALL=(ALL:ALL) NOPASSWD:ALL
    ```

    > **File:** /etc/sudoers

4. Create group admins

    ```sh
        sudo groupadd admins
    ```

5. Put admin user into admins group (replace *admin* with the name of the actual admin account, i.e. "hlpadm")

    ```sh
        usermod -a -G admins <admin>
    ```

6. Add users

    ```sh
        sudo useradd -m -g admins -s /bin/bash <username>
        sudo passwd <username>
    ```

### 2.4. System and environment settings

1. Set default editor: `sudo update-alternatives --config editor`
2. Set timezone: `sudo dpkg-reconfigure tzdata`
3. Change hostname:
    Edit **/etc/hostname** and `sudo hostname <hostname>` for current session

### 2.5. Virtualmin

Download and run the install script.
*Run in shell:*

```sh
    wget http://software.virtualmin.com/gpl/scripts/install.sh
    sudo /bin/sh install.sh --hostname <hostname>`
```

#### 2.5.1. Configure Webmin and Virtualmin

1. Navigate to https://hostname:10000 and follow the post installation wizard

2. Configuration

Follow this list, in order:

* Webmin

  * System

    * Bootup and Shutdown

      * Turn off Start at boot on proftpd
      * Turn off Start at boot on dovecot and dovecot.service
      * Turn off Start at boot on php7.0-fpm and php7.0-fpm.service

    * Disk Quotas
      * Disable quotas

    * Software packages updates
      * Check for every day
      * Set email
      * Install security updates

* Virtualmin

  * System settings

    * Features and plugins
      * Remove ProFTPD
      * Remove DAV login
      * Remove Mail
      * Remove Spam filtering
      * Remove Virus scanning
      * Remove BIND
      * Add SSL (and check default)

    * Virtualmin configuration
      * User Interface settings
        * Columns to show: Domain name, Username, SSL website
      * Default for new domains
        * Password field type: randomly generated password
      * SSL Settings
        * Request Let's Encrypt certificate at domain creation

    * Server templates
      * Default settings
                Reomove www. and mail. aliases from Apache website

  * Create virtual server
    * Create a virtual server with the domain - name pointing to Virtualmin

* Webmin

  * Webmin configuration

    * SSL Settings - Let's Encrypt
      * Insert hostname for certificate
      * Chose a different Apache virtual host (the one earlier created)

> **File:** crontab

### 2.6. Backup

#### 2.6.1. Disk

1. Create new virtual disk and connect it to the VM.
2. Create primary partition of type Linux (i.e. `fdisk /dev/sdc`)
3. Create the filesystem (i.e. `mkfs.ext4 /dev/sdc1`)
4. Put in /etc/fstab

    ```s
    /dev/sdc1    /mnt/backup    ext4    rw,user,exec    0    0
    ```

5. Mount `mount -a`
6. Change permission and ownership `chown root:admins -R /mnt/backup`, `chmod 774 -R /mnt/backup`

#### 2.6.2. Gitlab

If gitlab was installed, this is how to backup it:

1. Edit /etc/gitlab/gitlab.rb
    ```s
        gitlab_rails['manage_backup_path'] = true
        gitlab_rails['backup_path'] = "/mnt/backup/gitlab"
        gitlab_rails['backup_archive_permissions'] = 0644
        gitlab_rails['backup_keep_time'] = 86400 # 1 day, because we are setting up backup rotate later
    ```
2. Test gitlab backup: `sudo gitlab-rake gitlab:backup:create`
3. Create destination folder `mkdir /mnt/backup/gitlab`
4. Set permissions `chmod 700`, `chown git:admins`
5. Parent folder /mnt/backup must have +x flag

```crontab
# gitlab config
00 04 * * *  umask 0077; tar cfz /mnt/backup/gitlab/$(date "+etc-gitlab-\%s.tgz") -C / etc/gitlab

# gitlab repositories
30 04 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create CRON=1

```

#### 2.6.3. Virtualmin

1. Create backup destination, i.e. /mnt/backup/virtualmin, set owner to`root:admins` and `chmod 700`
2. Navigate to virtualmin Backup and Restore > Scheduled Backups > Add new backup schedule
3. Configure new backup as in the picture
    ![virtualmin-backup.png](virtualmin-backup.png)
4. Set Schedule and reporting **Email backup report to** to *op* email and **Scheduled backup time** to *Simple schedule to daily*

### 2.7. CRON jobs

### 2.7.1. Rkhunter

Run `crontab -e` to create a cron job for rkhunter

```sh
    0 4 * * * rkhunter --cronjob --update --quiet
```

> **File:** crontab

### 2.7.2. Backup Rotation

1. Configure backup_rotation.sh
2. Run it from crontab:

    ```s
    30 05 * * * /bin/bash /mnt/backup/backup_rotation.sh > /dev/null
    ```

    > **File:** crontab
