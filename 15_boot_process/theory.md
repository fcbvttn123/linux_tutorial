# BIOS

## What it is

- The BIOS is the first software (firmware) to run

## What it does

- Initialize and test the system hardware (CPU, RAM, Disk,..)

- After the hardware check, it **locates** and **hands off** control to the system bootloader

## How BIOS Finds the Bootloader (MBR)

- The location it checks depends on the disk's partitioning scheme: MBR (BIOS) or GPT (UEFI)

- After the hardware check, the BIOS selects the top drive in its boot priority

- Then, it reads the very first 512-byte sector of that physical drive

## UEFI Firmware

- `.efi` file is located on a dedicated **EFI System Partition** (ESP)

    - The `.efi` file is a single, compiled standalone executable binary file (like the `.exe` file)

    - It is compiled specifically to run in the UEFI pre-boot environment

- The **EFI System Partition**

    - This partition contains the bootloader for the installed OS

    - It is a small partition (typically 100MB–512MB) formatted as `FAT32` so the motherboard's `UEFI firmware` can read it natively


# Bootloader

## What it is

- A bootloader is a small program that loads the Linux kernel into memory and then executes it

- The most common Linux bootloader is `GRUB`

## What it does

- OS Selection: present a menu to boot into various OS

- Kernel Selection: it allows you to choose which version of the Linux kernel to load

- Passing Kernel Parameters: specifies crucial parameters that the kernel needs to start correctly


# Kernel

## What it does

- Once the bootloader has loaded the kernel into memory and passed the necessary parameters, the kernel takes control of the system

## Kernel Initialization and the Initramfs

- Problem

    - During boot-up, the kernel needs drivers to access hardware devices

    - But those drivers are on a storage device that the kernel can't access yet

    - Solution: temporary root filesystem

- In older systems, this was handled by an `initrd` (initial RAM disk)

    - The kernel load this disk image, find the necessary drivers
    
    - Then switch to the real root filesystem

- In modern systems, it is an `initramfs` (initial RAM filesystem)

    - Unlike `initrd`, `initramfs` is a `cpio` archive

    - It is unpacked into a temporary filesystem directly in memory

    - The `initramfs` contains just the essential modules the kernel needs to access the actual boot partition and other hardware

## Mounting the Boot Root Filesystem

- With the drivers loaded from `initramfs`, the kernel can now locate and mount the main boot `root filesystem`

- The location of this filesystem is typically passed as a parameter by the bootloader, which can be configured in files like `/etc/default/grub`

- With the `root filesystem` fully available, the kernel starts the very first user-space program: `init`

- The `init` program is responsible for bringing the rest of the system online