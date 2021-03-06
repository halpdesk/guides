# Debian 10 client installation guide

## 1. This document

This document is an installation guide for Debian 10 on developer machine.

### Revision history

| Author          | Change              | Time       |
|-----------------|---------------------|------------|
| Daniel Leppänen | First draft         | 2019-08-22 |

## 2. Steps

### 2.1. BIOS

- Enable secure boot
- Enable UEFI

### 2.2. Debian Live CD

1. Download Debian Live CD/USB

    [https://www.debian.org/CD/live/](https://www.debian.org/CD/live/)

2. Add ISO to USB using boot creator

    [https://www.pendrivelinux.com/yumi-multiboot-usb-creator/](https://www.pendrivelinux.com/yumi-multiboot-usb-creator/)

### 2.3. Boot with USB and install

1. Start with USB

    Boot Debian

2. Install debian using _Install Debian_ from the desktop

    - Follow the installation wizard.
    - Encrypt hard drive in _Partitions_ step. The ext4 file system should change to LUKS in the visual presentation for the partition table in the bottom.

3. Reboot system. Remove USB.

### 2.4. Post installation

1. Update apt sources

    ```sh
    nano /etc/apt/sources.list
    ```

    Update **buster** (not _updates_) distribution components with `contrib` and `non-free`:

    ```bash
    deb http://deb.debian.org/debian buster main contrib non-free
    deb-src http://deb.debian.org/debian buster main contrib non-free
    ```

2. Upgrade system

    ```sh
    apt update
    apt upgrade
    apt dist-upgrade
    ```

3. Install packages from repositories

    ```sh
    sudo apt install linux-headers-$(uname -r) \
    build-essential \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    gnupg2 \
    clamav-base \
    clamav-deamon \
    clamav \
    rkhunter \
    rsync \
    ncdu \
    xz-utils \
    htop \
    nmap \
    curl \
    locate \
    git \
    guake \
    gpick \

    ```

4. Drivers

    4.1. Wifi

      4.1.1. Install firmware

      ```sh
      apt install firmware-iwlwifi
      ```

      4.2.2. Start module

      ```sh
      modprobe -r iwlwifi
      modprobe iwlwifi
      ```

      4.1.3. Restart network manager

      ```sh
      service network-manager restart
      ```

    4.2. NVIDIA drivers

      First, see [[nvidia-driver]]

      4.2.1. Install nvidia detect:

      ```sh
      apt install nvidia-detect
      ```

      4.2.2. Download suggested drivers from [https://www.nvidia.com/en-us/drivers/unix/](https://www.nvidia.com/en-us/drivers/unix/)

      4.2.3. Install current headers and build-essential:

      ```sh
      apt install linux-headers-$(uname -r) build-essential`
      ```

      4.2.4. Disable nouveau driver:

      ```sh
      echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf
      ```

      4.2.5. Reboot to multi-user runlevel:

      ```sh
      systemctl set-default multi-user.target
      systemctl reboot
      ```

      4.2.6. Run installation

      ```sh
      bash NVIDIA-Linux-x86_64-390.116.run
      ```

      - Ignore CC version check
      - Yes for 32-bit compability
      - Install and overwrite existing filesort installation.
      - Yes to run nvidia-xconfig utility to automatically update

      4.2.7. Enable graphical boot and reboot

      ```bash
      systemctl set-default graphical.target
      systemctl reboot
      ```

5. Install other packages

    5.1. Google Chrome

    Download **.deb** package from [https://www.google.com/chrome/](https://www.google.com/chrome/)

    ```sh
    dpkg -i google-chrome-stable_current_amd64.deb
    ```

    5.2. VS Code

    Download **.deb** pacakge from [https://code.visualstudio.com/](https://code.visualstudio.com/)

    ```sh
    dpkg -i code_1.37.1-1565886362_amd64.deb
    ```

    5.3. Discord

    Download **.deb** pacakge from [https://discordapp.com/](https://discordapp.com/)

    ```sh
    dpkg -i discord-0.0.9.deb
    ```

    5.4. Slack

    Download **.deb** pacakge from [https://discordapp.com/](https://discordapp.com/)

    ```sh
    dpkg -i discord-0.0.9.deb
    ```

    5.5. Docker

    See up-to-date installation instructions here
    [https://docs.docker.com/install/linux/docker-ce/debian/](https://docs.docker.com/install/linux/docker-ce/debian/)

    5.6. Postman

    Download **.deb** pacakge from [https://www.getpostman.com/downloads/](https://www.getpostman.com/downloads/)

    ```sh
    mkdir ~/Apps
    mv Postman-linux-x64-7.3.6.tar.gz ~/Apps
    cd ~/Apps
    tar xvf Postman-linux-x64-7.3.6.tar.gz
    sudo ln -s $(cd ~; pwd)/Apps/Postman/Postman /usr/local/bin/postman
    ```

    5.7. Spotify

    See up-to-date installation instructions here
    [https://spotify.com/se/download/linux/](spotify.com/se/download/linux/)

    5.8. MPV

    Download **.deb** pacakge from [https://mpv.io/installation/](https://mpv.io/installation/)

    ```sh
    dpkg -i discord-0.0.9.deb
    ```

    Or add repositories: add below contents to **/apt/sourecs.list.d/mpv.list**

    ```sh
    deb [arch=amd64] https://non-gnu.uvt.nl/debian buster uvt
    ```

    Then download and add key to repository

    ```sh
    wget https://non-gnu.uvt.nl/debian/uvt_key.asc
    apt-key add - < uvt_key.asc
    ```

    > VLC is available throught the standard contrib repositories if you would rather use VLC.
