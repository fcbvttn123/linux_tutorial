# The `/dev` directory

- In Linux, every device (keyboard, disks,..) connected to your system is represented by a special file

- These **device files** are stored in the `/dev` directory


# Device Types

- Example

    ```bash
    $ ls -l /dev
    brw-rw----   1 root disk      8,0       Dec 20 20:13 sda
    crw-rw-rw-   1 root root      1,3       Dec 20 20:13 null
    srw-rw-rw-   1 root root        0       Dec 20 20:13 log
    prw-r--r--   1 root root        0       Dec 20 20:13 fdata
    ```

- Device Types

    - **Character Devices** (`c`): transfer data one character at a time

    - **Block Devices** (`b`): transfer data in large, fixed-size blocks (disks)

    - **Pipe Devices** (`p`): act like `c` but channel their output to another process instead of a physical device

    - **Socket Devices** (`s`)

- Device Numbers (e.g., `8,0`)

    - Major Number (8) => device driver

    - Minor Number (0) => tells the driver which specific instance of the device it is


# Device Name

- **SCSI/Storage Device**: `/dev/sda` the first drive, `/dev/sdb` the second drive, `/dev/sdb3` the second drive - third partition

- **Pseudo-Devices**: special files which are NOT physical hardware (character devices)


# `lsusb`, `lspci`, `lsscsi`

- `lsusb` => list USB devices

- `lspci` => list PCI devices

- `lsscsi` => list SCSI and SATA Devices


# The `udev` system

- In older systems, the `/dev` directory was static

    - This meant that device files for all possible hardware were created during installation

    - This approach led to a cluttered `/dev` filled with unused device files for hardware that wasn't even present

    - Device names could change between reboots depending on the order the kernel detected them

- The `udev` system dynamically creates and removes device files for us depending on whether or not they are connected

- There is a `udevd` daemon that is running on the system, and it listens for messages from the kernel about devices connected to the system