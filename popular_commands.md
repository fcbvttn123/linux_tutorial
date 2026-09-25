# Contents

- [Contents](#contents)
- [Bandit Game](#bandit-game)
- [User and Group Management](#user-and-group-management)
  - [Core Files](#core-files)
  - [User Management](#user-management)
  - [Group Management](#group-management)
  - [Auditing \& Inspecting Access](#auditing--inspecting-access)
  - [Owner / Permission](#owner--permission)
- [Processes](#processes)
  - [`ps`, `top`](#ps-top)
  - [`nice`](#nice)
  - [Signal](#signal)
  - [Job Control](#job-control)
  - [RAM, Disk](#ram-disk)
- [Package Management](#package-management)
- [Disks, Filesystems](#disks-filesystems)
  - [Check file system](#check-file-system)
  - [Disk Space](#disk-space)
  - [List Block Devices](#list-block-devices)
  - [Get Partition UUID](#get-partition-uuid)
  - [Partition Table Details](#partition-table-details)
  - [Partition Management](#partition-management)
  - [Format, Mount](#format-mount)
- [systemd](#systemd)




# Bandit Game

```bash
# dash file
cat .\-

# advanced "find" command
# the "-exec file {} +" part tells find to run the "file" command on every file it discovers
find /path/to/folder -maxdepth 1 -type f -exec file {} + | grep ': ASCII text' | cut -d: -f1
find /path/to/folder -type f -user bandit7 -group bandit6 -size 33c 2> /dev/null
find /path/to/folder -type f -size 1033c ! -executable -exec file {} + | grep ': ASCII text' | cut -d: -f1

# decode a string of Base64-encoded text
base64 -d data.txt

# create a temporary workspace
cd $(mktemp -d)

# convert the hexdump back into binary
xxd -r data.txt data1

# decompress Gzip: add .gz extension and extract
mv data1 data2.gz
gzip -d data2.gz
file data2
# decompress Bzip2: extract bzip2 archive directly
bzip2 -d data2
# extract tar archive
tar -xf data3

# use the private key to SSH into localhost
ssh -i sshkey.private bandit14@localhost -p 2220

# "nc": networking utility used for reading and writing data across network connections using TCP or UDP
cat /etc/bandit_pass/bandit14 | nc localhost 30000

# "open ssl": send data over an SSL/TLS encrypted connection
openssl s_client -connect localhost:30001

# port scanning
nmap -sV -p 31000-32000 localhost

# execute the binary directly
./binary-file
```




# User and Group Management

## Core Files

- `/etc/passwd`: Stores user account details (UID, GID, home directory, shell)

- `/etc/shadow`: Stores encrypted user passwords and password expiration policies

- `/etc/group`: Stores group definitions and member lists

## User Management

```bash
# -m creates the home directory /home/alex
sudo useradd -m alex
# set password for user
sudo passwd alex
# modfy user, -s changes the user's default login shell
sudo usermod -s /bin/zsh alex
# lock user
sudo usermod -L alex
# delete user, -r removes home directory and mail spool
sudo userdel -r alex
# check which groups a user belongs to
groups username
```

## Group Management

```bash
# create group
sudo groupadd developers
# add user to group, omitting -a removes other groups
sudo usermod -aG developers alex
# drop user from the specified group
sudo gpasswd -d alex developers
# remove group (cannot delete primary group of active user)
sudo groupdel developers
# check which groups a user belongs to
groups username
```

## Auditing & Inspecting Access

```bash
# check user details: UID, primary group, and supplementary groups
id alex
# list group members
getent group developers
```

## Owner / Permission

```bash
# change owner
sudo chown alex report.txt
# change group
sudo chgrp developers report.txt
# change both owner and group
sudo chown alex:developers report.txt
# apply ownership to directory and all contents
sudo chown -R alex:developers /var/www/
# add the execute permission for the user
chmod u+x myfile
# remove permission for group
chmod g-w myfile
# modify multiple permission at once
chmod ug+w myfile
# changing permission with number: 4 (r), 2 (w), 1 (x)
chmod 755 myfile
```




# Processes

## `ps`, `top`

```bash
# monitor processes
$ ps -eo pid,ppid,stat,cmd
  PID   PPID S CMD
12345   6789 Z [myprogram] <defunct>
12456   6789 Z [worker] <defunct>

# top
top - 18:06:26 up 6 days,  4:07,  2 users,  load average: 0.92, 0.62, 0.59
Tasks: 389 total,   1 running, 387 sleeping,   0 stopped,   1 zombie
%Cpu(s):  1.8 us,  0.4 sy,  0.0 ni, 97.6 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:  32870888 total, 27467976 used,  5402912 free,   518808 buffers
KiB Swap: 33480700 total,    39892 used, 33440808 free. 19454152 cached Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 6675 patty    20   0 1731472 520960  30876 S   8.3  1.6 160:24.79 chrome
 6926 patty    20   0  935888 163456  25576 S   4.3  0.5   5:28.13 chrome
```

## `nice`

```bash
# nice/renice
nice -n 5 apt upgrade
renice 10 -p 3245
```

## Signal

```bash
# signal: terminate gracefully (SIGTERM 15)
kill PID
# signal: terminate forcefully (SIFKILL 9)
kill -KILL PID
# signal: reload configuration for programs (SIGHUP 1)
kill -HUP PID
# signal: suspend process (SIGSTOP 19)
kill -STOP PID
```

## Job Control

```bash
# job control: start command in background
$ sleep 1000 &
[1] 4521
# job control: show shell's job
$ jobs
[1]+  Running                 sleep 1000 &
[2]-  Running                 ping google.com &
# job control: bring job to foreground
$ fg %1
ping google.com
64 bytes from 142.250.190.46: icmp_seq=5 ttl=116 time=14.2 ms
64 bytes from 142.250.190.46: icmp_seq=6 ttl=116 time=12.1 ms
# job control: move job to background
$ bg %1
[1]- sleep 500 &
# job control: keep running after logout
$ nohup ./backup.sh &
nohup: ignoring input and appending output to 'nohup.out'
[1] 5321
```

## RAM, Disk

```bash
# RAM: focus on available number, not free
$ free -h
               total        used        free      shared  buff/cache   available
Mem:            15Gi        6.2Gi       1.1Gi       512Mi       7.8Gi       8.9Gi
Swap:          2.0Gi        256Mi       1.8Gi

# iostat: is my server slow because the storage/disk is slow?
$ iostat
Linux 6.8.0 (server01)  09/08/2026  _x86_64_
avg-cpu:  %user   %system   %iowait  %steal   %idle
           12.50      3.20      8.40     0.00    75.90
Device             tps   kB_read/s   kB_wrtn/s   kB_read   kB_wrtn
sda             45.20       850.40       320.10     1024000     384000
nvme0n1         120.50      4500.20      2100.40     5400240    2520480

# disk space: how much space is available on the filesystem?
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/nvme0n1p2  100G   72G   23G  76% /
/dev/nvme0n1p1  512M  120M  392M  24% /boot
tmpfs            16G  2.1M   16G   1% /run
# disk space: which files/directories are using that space?
$ du -sh /*
2.4G    /bin
8.7G    /home
1.2G    /opt
65G     /var
4.1G    /usr
20M     /tmp
```




# Package Management
```bash
# updating & upgrading packages
sudo apt update
sudo apt upgrade
sudo apt full-upgrade

# finding & inspecting packages
apt search <package>
apt show <package>
apt list --installed

# installing & removing packages
sudo apt install <package>
sudo apt remove <package>

# low-level package utility
sudo dpkg -i package.deb
sudo apt --fix-broken install
dpkg -l # list installed pkg
dpkg -S /path/to/file # find which package owns a file
```




# Disks, Filesystems

## Check file system
```bash
# check file system
$ df -T
Filesystem     Type     1K-blocks    Used Available Use% Mounted on
/dev/sda1      ext4       6461592 2402708   3707604  40% /
udev           devtmpfs    501356       4    501352   1% /dev
tmpfs          tmpfs       102544    1068    101476   2% /run
/dev/sda6      xfs       13752320  460112  13292208   4% /home
```

## Disk Space
```bash
# disk space: how much space is available on the filesystem?
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/nvme0n1p2  100G   72G   23G  76% /
/dev/nvme0n1p1  512M  120M  392M  24% /boot
tmpfs            16G  2.1M   16G   1% /run
# disk space: which files/directories are using that space?
$ du -sh /*
2.4G    /bin
8.7G    /home
1.2G    /opt
65G     /var
4.1G    /usr
20M     /tmp
```

## List Block Devices
```bash
# list block devices
$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda           8:0    0 223.6G  0 disk 
├─sda1        8:1    0   512M  0 part /boot/efi
├─sda2        8:2    0  1.5G   0 part /boot
└─sda3        8:3    0 221.6G  0 part 
  ├─vg-root 253:0    0    40G  0 lvm  /
  ├─vg-swap 253:1    0     4G  0 lvm  [SWAP]
  └─vg-home 253:2    0 177.6G  0 lvm  /home
sr0          11:0    1  1024M  0 rom
```

## Get Partition UUID

```bash
# find the exact UUID of a partition to configure persistent automounting in /etc/fstab
$ sudo blkid
/dev/sda1: UUID="A1B2-C3D4" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="000a1234-01"
/dev/sda2: UUID="5f3e9c12-38fa-4bfa-91b4-b2586e1141bc" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="000a1234-02"
/dev/sdb1: LABEL="DataDisk" UUID="7fa9c421-0054-4555-b0ca-b470a97a3d84" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="111b5678-01"
/dev/sdc1: UUID="2026-03-02-12-00-00-00" LABEL="Ubuntu-Server" TYPE="iso9660" PTUUID="4af3c21b" PTTYPE="dos"
/dev/nvme0n1p1: UUID="E4F2-89AC" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="d123e456-bcde-4789-ba12-3456789abcdef"
```

## Partition Table Details

```bash
# display detailed low-level partition table information for all connected disks
$ sudo fdisk -l

Disk /dev/sda: 500 GiB, 536870912000 bytes, 1048576000 sectors
Disk model: Samsung SSD 870
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

Device       Start        End   Sectors   Size Type
/dev/sda1     2048     1050623   1048576   512M EFI System
/dev/sda2  1050624    84903935  83853312    40G Linux filesystem
/dev/sda3 84903936 1048575966 959071031 457.5G Linux filesystem


Disk /dev/sdb: 1 TiB, 1099511627776 bytes, 2147483648 sectors
Disk model: WD Blue
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

Device       Start        End     Sectors  Size Type
/dev/sdb1     2048 2147483647 2147481600    1T Linux filesystem
```

## Partition Management

```bash
# create a disk partition (MBR)
sudo fdisk /dev/sdb
# create a disk partition (GPT)
sudo parted /dev/sdb print
```

## Format, Mount

```bash
# create an ext4 filesystem
sudo mkfs.ext4 /dev/sdb1
# create an XFS filesystem
sudo mkfs.xfs /dev/sdb1

# create a mount point and mount a partition
sudo mkdir -p /mnt/data
sudo mount /dev/sdb1 /mnt/data
# unmount
sudo umount /mnt/data

# mount by UUID
sudo blkid /dev/sdb1
```




# systemd

```bash
# systemctl
systemctl status <service>
systemctl start <service>
systemctl stop <service>
systemctl restart <service>
systemctl enable <service>
systemctl disable <service>
systemctl enable --now <service>
systemctl is-active <service>
systemctl is-enabled <service>
systemctl list-units
systemctl list-units --type=service
systemctl list-unit-files
systemctl get-default
systemctl list-dependencies <service>
systemctl cat <service>
systemctl show <service>
sudo systemctl daemon-reload

# logs
journalctl
journalctl -u <service>
journalctl -u <service> -f
journalctl -b
journalctl -b -1
```