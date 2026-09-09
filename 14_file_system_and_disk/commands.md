```bash

# check FS on the system (`df`)
pete@icebox:~$ df -T
Filesystem     Type     1K-blocks    Used Available Use% Mounted on
/dev/sda1      ext4       6461592 2402708   3707604  40% /
udev           devtmpfs    501356       4    501352   1% /dev
tmpfs          tmpfs       102544    1068    101476   2% /run
/dev/sda6      xfs       13752320  460112  13292208   4% /home


# filesystem repair
sudo fsck /dev/sda


# list existing partitions
sudo parted -l
# launch interactive mode
# you will enter the parted tool's shell, where you can run commands to manage the partitions
sudo parted
# select a deivce
select /dev/sdb
# view the partition table
(parted) print
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:
Number  Start   End     Size    Type      File system  Flags
 1      1049kB  10.7GB  10.7GB  primary   ext4         boot
# create partition
# creates a primary partition formatted with ext4, starting at 1MB and ending at 5000MB
mkpart primary ext4 1MB 5000MB
# resize a partition
# resizes partition number 1 to end at the 8000MB mark
resizepart 1 8000MB


# create filesystems
sudo mkfs -t ext4 /dev/sdb2


# create a mount point
sudo mkdir /mydrive
# attach the device
sudo mount -t ext4 /dev/sdb2 /mydrive
# unmount a FS
sudo umount /mydrive
# unmount using the device name
sudo umount /dev/sdb2


# using UUIDs for Stable Mounting (a device name like /dev/sdb2 could change between reboots)
pete@icebox:~$ sudo blkid
/dev/sda1: UUID="130b882f-7d79-436d-a096-1e594c92bb76" TYPE="ext4"
/dev/sda5: UUID="22c3d34b-467e-467c-b44d-f03803c2c526" TYPE="swap"
/dev/sda6: UUID="78d203a0-7c18-49bd-9e07-54f44cdb5726" TYPE="xfs"
# mount device using UUID
sudo mount UUID=130b882f-7d79-436d-a096-1e594c92bb76 /mydrive


# storage management with df (disk free)
pete@icebox:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       6.2G  2.3G  3.6G  40% /
# identify which files or directories are consuming the most space with du (disk usage)
du -h
# check inode usage: you can run out of inodes even if you have free disk space
pete@icebox:~$ df -i
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
/dev/sda1      4128768 128768 4000000    4% /