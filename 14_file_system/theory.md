# Overview

## File System vs Partition

- Inside a **partition**, you can create a **filesystem** or dedicate it to other purposes, such as swap space

- The FS consists of a database to manage files and the files themselves

## Check FS on the system (`df`)

```bash
pete@icebox:~$ df -T
Filesystem     Type     1K-blocks    Used Available Use% Mounted on
/dev/sda1      ext4       6461592 2402708   3707604  40% /
udev           devtmpfs    501356       4    501352   1% /dev
tmpfs          tmpfs       102544    1068    101476   2% /run
/dev/sda6      xfs       13752320  460112  13292208   4% /home
```


# Some Important Folders

## Essential System Directories

- `/bin`: contains essential command-line programs (binaries) available to all users (`ls`, `cp`,...)

- `/sbin`: contains essential system binaries, which can only be run by the root user

- `/etc`: contains configuration files for the operating system and installed applications

- `/lib`: contains essential shared library files that `/bin` and `/sbin` depend on to function correctly

- `/boot`: stores the files required for the system's boot process, including the Linux kernel and the boot loader files

## User and Application Data

- `/home`, `/root`

- `/opt`: reserved for optional or third-party application software packages

- `/usr`: contains user-installed software and utilities

## Device and Mount Points

- `/dev`: contains special device files that represent hardware components

- `/media`: a standard mount point for removable media like USB drives, SD cards, and CD-ROMs

- `/mnt`: a generic mount point for temporarily mounting filesystems


# VFS and Journal

## The Role of the Virtual File System

- With so many different implementations available, apps need a consistent way to interact with them

- The VFS is an abstraction layer in the Linux kernel that sits between apps and the various filesystems

## Journaling for Data Integrity

- `Journaling` is used for the system crash while transfering file (e.g., copying a large file when the computer suddenly loses power)

- Before performing a write operation, it first records the intended changes in a special log file - `journal`


# Anatomy of a disk

## The Partition Table

- The Partition Table is a component of a disk

- It tells the OS how the disk is partitioned

    - Where each partition begins and ends

    - Which partitions are bootable
    
    - What sectors of the disk are allocated to each partition

- 2 partition table schemes: `MBR` (Master Boot Record) and `GPT` (GUID Partition Table)

## MBR Partitions (traditional)

- It supports `primary`, `extended`, and `logical` partitions

- MBR has a limit of four primary partitions

- To create more partitions, one primary partition must be designated as an extended partition (only one is allowed per disk). Within this extended partition, you can create multiple logical partitions, which function like any other partition

- It supports disks up to 2 terabytes in size

- Example

    ```bash
    pete@icebox:~$ sudo parted -l
    Model: Seagate (scsi)
    Disk /dev/sda: 21.5GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos

    Number  Start   End     Size    Type      File system     Flags
    1      1049kB  6860MB  6859MB  primary   ext4            boot
    2      6861MB  21.5GB  14.6GB  extended
    5      6861MB  7380MB  519MB   logical   linux-swap(v1)
    6      7381MB  21.5GB  14.1GB  logical   xfs
    ```

## GPT Partitions (modern)

- It has only one type of partition, and you can create a large number of them

- Each partition is assigned a Globally Unique Identifier (GUID)

- GPT is commonly used with `UEFI-based booting systems`

- Example

    ```bash
    Model: Thumb Drive (scsi)
    Disk /dev/sdb: 4041MB
    Sector size (logical/physical): 512B/512B
    Partition Table: gpt

    Number  Start   End     Size     File system  Name        Flags
    1      17.4kB  1000MB  1000MB                first
    2      1000MB  4040MB  3040MB                second
    ```


# Filesystem Structure

## Boot block

- Located in the first few sectors of a FS

- This block is not used by the FS itself. Instead, it contains information used to boot the OS

- Only one boot block is needed per OS

- Other partitions may have boot blocks, they often go unused

## Superblock

- This is a single block following the boot block that contains metadata about the filesystem

- Metadata: inode table size, logical block size, FS Size

## Inode Table

- This is the database that manages files and directories

- Each file or directory has a unique entry in the inode table

## Data blocks

- This is where the actual content of your files and directories is stored


# `/etc/fstab`

## What is it

- The `/etc/fstab` file is a system configuration file

- The system consults this file during startup to determine which filesystems to mount automatically

## The fstab File Structure

```bash
pete@icebox:~$ cat /etc/fstab
UUID=130b882f-7d79-436d-a096-1e594c92bb76 /               ext4    relatime,errors=remount-ro 0       1
UUID=78d203a0-7c18-49bd-9e07-54f44cdb5726 /home           xfs     relatime                   0       2
UUID=22c3d34b-467e-467c-b44d-f03803c2c526 none            swap    sw                         0       0
```