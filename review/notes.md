# What happen when we install a package (`apt install openssh-server`)

## Don't place everything into a single folder

- It starts `a series of actions` across multiple system directories rather than placing everything into a single folder

- Unlike OS platforms that bundle applications into a single folder (like `C:\Program Files` or `/Applications`)

- Linux distributions follow the **Filesystem Hierarchy Standard (FHS)**, placing specific file types in dedicated system locations

## Where new files are placed

- `/usr/sbin/` (or `/usr/bin/`): executable binaries - `/usr/sbin/sshd` (the daemon executable)

- `/etc/`: configuration files - `/etc/ssh/sshd_config`, `/etc/ssh/ssh_host_*_key`

- `/lib/systemd/system/`: service unit definitions - `/lib/systemd/system/ssh.service`

- `/var/lib/` & `/var/log/`: dynamic data & logs - `/var/lib/apt/`, `/var/log/auth.log`

- `/usr/share/doc/` & `/usr/share/man/`

## The Step-by-Step Installation Process

- Dependency Resolution: `apt` reads its cache to figure out what dependencies are needed (e.g., `openssh-client`, `libssl3`, `ucf`)

- Package Download: `apt` downloads the individual `.deb` files for the requested package and any missing dependencies, placing them into `/var/cache/apt/archives/`

- Unpacking: `dpkg` opens each `.deb` archive and places the contents into their destinations (`/etc`, `/usr/bin`, etc.)