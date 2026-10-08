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




$ sudo apt update
$ dpkg -s openssh-server # check if a pkg is already installed
$ sudo apt install openssh-server

$ systemctl status ssh
$ systemctl start ssh
$ sudo nano /etc/ssh/sshd_config
    PermitRootLogin no
$ sudo systemctl restart ssh

# -x log with explanatory text, -e end of the log buffer
$ sudo journalctl -xe
# view logs since last boot
sudo journalctl -b
# filter log for a specific service
sudo journalctl -u nginx.service -e
