# User Management

```bash
$ sudo useradd <username> # no home dir
$ sudo adduser <username> # create home dir
$ sudo passwd <username>
$ sudo chsh -s /path/to/shell username
$ cat /etc/passwd
<username>:x:1001:1001::/home/<username>:/bin/sh

$ sudo usermod -aG sudo <username>
$ groups <username>
$ id
uid=1002(admin) gid=1002(admin) groups=1002(admin),27(sudo)
$ getent group sudo
sudo:x:27:david,adminuser,admin

$ su - <username>

sudo mkdir -p /home/<username>
sudo cp -r /etc/skel/. /home/<username>/
sudo chown -R <username>:<username> /home/<username>

$ touch <filename>.txt
$ sudo chown <username>:<username> <filename>.txt
```


# Package Management

```bash
$ sudo apt update
$ dpkg -s openssh-server # check if a pkg is already installed
$ sudo apt install openssh-server
```


# systemd

```bash

$ systemctl status ssh
$ systemctl start ssh
$ sudo nano /etc/ssh/sshd_config
    PermitRootLogin no
$ sudo systemctl restart ssh

# -x log with explanatory text, -e end of the log buffer
$ sudo journalctl -xe
# show recent system messages
sudo journalctl -n 100
# view logs since last boot
sudo journalctl -b
# filter log for a specific service
sudo journalctl -u nginx.service -e
# show warnings and more severe messages
sudo journalctl -p warning
```


# System Check

```bash
# show OS and kernel information
cat /etc/os-release
uname -a
# show hostname and system information
hostnamectl
# show network interfaces and addresses
ip -br address
# show the routing table
ip route
# check DNS/network connectivity
ping -c 4 ubuntu.com
```