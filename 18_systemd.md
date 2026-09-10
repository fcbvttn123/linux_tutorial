# What is `systemd`

- `systemd` is the system and service manager

- It is typically the first userspace process started during boot

    ```bash
    Kernel
    │
    ▼
    systemd (PID 1)
    │
    ├── networking
    ├── SSH
    ├── logging
    ├── cron/timers
    ├── mounts
    ├── databases
    └── other services
    ```

    ```bash
    $ ps -p 1 -o pid,comm,args
    PID COMMAND  COMMAND
      1 systemd  /sbin/init
    ```




# `systemctl`

- It is the main command for interacting with `systemd`

- For example, `SSH`: `systemctl status sshd`

## Start, restart, and stop services

```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
```

## Enable vs start

- Start the service immediately: `sudo systemctl start nginx`

- Configures the service to start automatically during boot: `sudo systemctl enable nginx`

- Prevent automatic startup: `sudo systemctl disable nginx`




# What is a `unit`

- `systemd` manages things called **units**

- `systemd` units are physical text files stored on the filesystem

- Package managers like `apt` install them automatically (`sudo apt install openssh-server`)

- There are several types:

    ```bash
    .service
    .socket
    .target
    .mount
    .automount
    .timer
    .path
    .device
    ```

- As a beginner, focus primarily on: `.service`, `.target`, `.timer`, `.mount`

- List services

    ```bash
    $ systemctl list-units --type=service
    UNIT                    LOAD   ACTIVE SUB     DESCRIPTION
    accounts-daemon.service loaded active running Accounts Service
    apparmor.service        loaded active exited  AppArmor initialization
    cron.service            loaded active running Regular background program processing daemon
    dbus.service            loaded active running D-Bus System Message Bus
    getty@tty1.service      loaded active running Getty on tty1
    ssh.service             loaded active running OpenBSD Secure Shell server

    LOAD   = Reflects whether the unit definition was properly loaded.
    ACTIVE = The high-level unit activation state.
    SUB    = The low-level unit activation state.
    ```




# `.service` units

- These represent services/programs

- Examples: `sshd.service`, `nginx.service`, `docker.service`, `cron.service`




# `.target` units

- Targets are essentially groups/states of units




# systemd log - `journalctl`

```bash
# logs for a particular service
sudo journalctl -u ssh.service
-- Logs begin at Mon 2026-09-07 08:32:11 EDT, end at Wed 2026-09-09 21:15:43 EDT. --
Sep 09 14:22:01 server-01 systemd[1]: Starting OpenBSD Secure Shell server...
Sep 09 14:22:01 server-01 sshd[1043]: Server listening on 0.0.0.0 port 22.
Sep 09 14:22:01 server-01 sshd[1043]: Server listening on :: port 22.
Sep 09 14:22:01 server-01 systemd[1]: Started OpenBSD Secure Shell server.
Sep 09 14:25:32 server-01 sshd[1124]: Accepted publickey for ubuntu from 192.168.1.50 port 54322 ssh2: RSA SHA256:abc123xyz...
Sep 09 14:25:32 server-01 sshd[1124]: pam_unix(sshd:session): session opened for user ubuntu by (uid=0)
Sep 09 21:02:15 server-01 sshd[2589]: Invalid user admin from 203.0.113.5 port 41223
Sep 09 21:02:17 server-01 sshd[2589]: Connection closed by authenticating user admin 203.0.113.5 port 41223 [preauth]

# recent log
journalctl --since "1 hour ago"
```