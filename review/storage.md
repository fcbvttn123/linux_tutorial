# Learning Path

```bash
1. Physical disks
       ↓
2. Partitions: MBR / GPT
       ↓
3. Filesystems: ext4 / XFS
       ↓
4. Mounting
       ↓
5. LVM
       ↓
6. Device Mapper
       ↓
7. RAID
       ↓
8. Encryption (LUKS/dm-crypt)
       ↓
9. Multipath / SAN
       ↓
10. Troubleshooting complex storage stacks
```


# Physical Disk Model

- Learn what these actually mean:

    ```bash
    /dev/sda
    /dev/sdb
    /dev/nvme0n1
    ```

- Then

    ```bash
    /dev/sdb
    ├── /dev/sdb1
    ├── /dev/sdb2
    └── /dev/sdb3
    ```

- Understand:

    - disk vs partition

    - sectors and blocks

    - MBR vs GPT

    - partition tables

    - filesystem vs partition

    - /dev and device nodes


# File System

- ext4

- XFS

- filesystem metadata

- superblocks

- inodes

- blocks

- mount

- /etc/fstab

- UUIDs


# LVM

- Learn

    ```bash
    Physical Volume (PV)
            ↓
    Volume Group (VG)
            ↓
    Logical Volume (LV)
            ↓
    Filesystem
    ```

- Then

    ```bash
    pvs
    vgs
    lvs
    pvdisplay
    vgdisplay
    lvdisplay
    ```


# Device Mapper

- LVM actually relies on the Linux device-mapper subsystem

- You'll encounter things like:

    ```bash
    /dev/mapper/vg0-var
    /dev/mapper/cryptroot
    /dev/dm-0
    /dev/dm-1
    ```

- Learn the relationship:

    ```bash
    Filesystem
        ↓
    Logical Volume
        ↓
    Device Mapper
        ↓
    Physical storage
    ```


# Encryption

```bash
/dev/sdb
   │
   ▼
/dev/sdb1
   │
   ▼
LUKS encrypted device
   │
   ▼
/dev/mapper/secret
   │
   ▼
filesystem
   │
   ▼
/data
```


# RAID

- Software RAID

    ```bash
    /dev/sdb
    /dev/sdc
    /dev/sdd
    /dev/sde
        │
        ▼
    mdadm
        │
        ▼
    /dev/md0
        │
        ▼
    filesystem
    ```

- Commands

    ```bash
    cat /proc/mdstat
    mdadm
    lsblk
    ```

- And eventually you can encounter stacks like:

    ```bash
    NVMe disks
    ↓
    RAID
    ↓
    LVM
    ↓
    LUKS
    ↓
    filesystem
    ↓
    mount point
    ```


# Lab

- Create a Linux VM and experiment with virtual disks

    ```bash
    /dev/sda   operating system
    /dev/sdb   20 GB
    /dev/sdc   20 GB
    ```