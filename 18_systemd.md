# Contents

- [Contents](#contents)
- [What is `systemd`](#what-is-systemd)
- [`systemctl`](#systemctl)
  - [Start, restart, and stop services](#start-restart-and-stop-services)
  - [Enable vs start](#enable-vs-start)
- [What is a `unit`](#what-is-a-unit)
- [Understanding service files](#understanding-service-files)
  - [What they are](#what-they-are)
  - [Core Structure of a Service File](#core-structure-of-a-service-file)
  - [Basic Management Workflow](#basic-management-workflow)
- [Dependencies](#dependencies)
  - [Requirement Dependencies](#requirement-dependencies)
  - [Ordering Dependencies](#ordering-dependencies)
  - [Special Targets \& System Triggers](#special-targets--system-triggers)
- [`.service` units](#service-units)
- [`.target` units](#target-units)
  - [Groups](#groups)
  - [Desired State](#desired-state)
  - [`.target` file](#target-file)
  - [`default.target`](#defaulttarget)
- [`.timer` units](#timer-units)
- [systemd log - `journalctl`](#systemd-log---journalctl)
- [Commands](#commands)




# What is `systemd`

- `systemd` is the system and service manager (`d` for `daemon`)

- **Daemons vs Processes**: a process needs to be started by user, daemons are processes running in the background without user needed to start them

- **Daemon Processes** have `d` at the end of the name: `sshd`, `httpd`, `ntpd`...

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

- `systemd` manages things called **units** (different types of deamon)

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




# Understanding service files

## What they are

- A `.service` file is a plain-text configuration file that tells Linux how, when, and under what conditions to run a background process (`daemon`)

- Systemd looks for these unit files in two primary locations:

    - `/lib/systemd/system/` or `/usr/lib/systemd/system/`: System-installed default services (managed by package managers)

    - `/etc/systemd/system/`: Custom or user-defined services (takes precedence over defaults)

## Core Structure of a Service File

```bash
[Unit]
Description=My Custom Application Service
After=network.target
Wants=network-online.target

[Service]
Type=simple
User=appuser
Group=appgroup
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/main.py
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

- 3 main sections: `[Unit]`, `[Service]`, and `[Install]`

- `[Unit]`: define generic metadata about the service and its dependencies

    - Key Directives: `Description`, `Documentation`, `After`, `Requires`, `Wants`

    - `Description`: A human-readable name shown in log outputs (systemctl status)

    - `After`: Defines execution ordering. `After=network.target` means this service starts after network services start, but it doesn't strictly depend on them

    - `Requires`: Strict dependency. If the named unit fails or stops, this service stops too

    - `Wants`: Soft dependency. Systemd will attempt to start the listed unit, but will continue starting your service even if that unit fails

- `[Service]`: define the actual execution details - how to launch, run, reload, and manage the process

    - Key Directives: `Type`, `ExecStart`, `ExecReload`, `Restart`, `User`, `Environment`

    - `Type`: Tells systemd how the process behaves

        - `simple` (default): Systemd assumes the service starts immediately upon launching ExecStart

        - `forking`: Used when the process spawns a child process and the main process exits (typical for legacy daemons)

        - `oneshot`: The process executes, finishes, and exits (useful for startup scripts)

    - `ExecStart`: The full path to the executable command to run

    - `ExecReload` / `ExecStop`: Commands to execute when reloading or stopping the service

    - `User` / `Group`: Runs the process under a non-root service account for security

    - `Restart`: Defines recovery behavior (always, on-failure, no)

    - `RestartSec`: How long to wait before attempting a restart

- `[Install]`: define system hooks for enablement (systemctl enable/disable) and target binding

    - Key Directives: `WantedBy`, `RequiredBy`, `Alias`

    - Enablement and Target States

    - `WantedBy`: Defines which target (system state) enables this service when turned on at boot
    
    - `multi-user.target` is standard for non-graphical multi-user boot states (similar to traditional runlevel 3)

## Basic Management Workflow

- Once a service file is placed in `/etc/systemd/system/my-service.service`

    ```bash
    # Tell systemd to re-scan unit files after creating or editing
    sudo systemctl daemon-reload
    # Start the service immediately
    sudo systemctl start my-service
    # Enable the service to launch at boot (creates symlinks in target directories)
    sudo systemctl enable my-service
    # Inspect status, logs, and process IDs
    sudo systemctl status my-service
    ```




# Dependencies

## Requirement Dependencies

- **Requirement directives** tell `systemd` which other units must exist or be active alongside your service

- `Wants`= (Soft Dependency - Recommended)

    - Systemd will attempt to start the listed units when starting your service

    - If the wanted unit fails to start or is missing, your service still runs anyway

    - Use case: Non-critical helper services, optional logging tools

- `Requires`= (Hard Dependency)

    - Systemd will start the listed units when starting your service

    - If any listed unit fails to start or stops later, your service will immediately fail or shut down

    - Use case: A web app that physically cannot launch without an attached database local socket

- `BindsTo`= (Strict Lifecycle Lock)

    - Similar to Requires=, but stronger: if the linked unit is stopped, restarted, or dies unexpectedly at any point, your service dies instantly with it

    - Use case: Services tied directly to specific hardware devices (like a network interface or USB device)

- `Conflicts`= (Mutually Exclusive)

    - If your service starts, any active unit listed in Conflicts= will be stopped immediately (and vice-versa)

    - Use case: Preventing two firewall tools or network managers from running simultaneously

## Ordering Dependencies

- Crucial Rule: `Wants=` and `Requires=` do NOT control execution order

    - They only tell `systemd` to start the services concurrently
    
    - If service B requires service A, `systemd` will launch both at the exact same millisecond unless ordering is specified

- `After=`

    - Ensures your unit starts after the listed units have finished starting

    - Example: After=network-online.target guarantees network configuration is completely established before your service attempts to bind ports

- `Before=`

    - Ensures your unit starts and settles before the listed units begin launching

## Special Targets & System Triggers

- Systemd uses Targets (groupings of unit files) to coordinate broad system states

- `network.target`: network stack is initialized (does not guarantee an IP address is assigned)

- `network-online.target`: network interfaces are fully up and an IP address is bound. Use this for services requiring external network access

- `multi-user.target`: system is fully booted into multi-user CLI mode

- `sockets.target`: system sockets are bound and listening




# `.service` units

- These represent services/programs

- Examples: `sshd.service`, `nginx.service`, `docker.service`, `cron.service`




# `.target` units

- `.target` = a named system state/milestone assembled from dependencies on other units

- `.service`  → "Do this thing"

- `.target`   → "I want the system to have reached this state"

## Groups

- A target can group these together:

    ```bash
    server.target
        ├── nginx.service
        ├── postgresql.service
        └── sshd.service
    ```

- Then instead of telling systemd:

    ```bash
    systemctl start nginx
    systemctl start postgresql
    systemctl start sshd
    ```

- You can tell it `systemctl start server.target`

## Desired State

- Targets are more than just "groups"

- A target generally represents a desired state of the system, or milestones that systemd can try to reach

- Example: `graphical.target` means the system should be in a state where the graphical desktop environment is available

    ```bash
    graphical.target
        |
        +-- NetworkManager.service
        +-- display-manager.service
        +-- various other services
    ```

## `.target` file

```bash
[Unit]
Description=My Server Target
Requires=nginx.service
Requires=postgresql.service
```

## `default.target`

- The state the system normally boot into

- Example: `default.target -> graphical.target` or `default.target -> multi-user.target`

- Check it: `systemctl get-default`

    ```bash
    $ systemctl get-default
    graphical.target
    ```




# `.timer` units




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




# Commands

```bash
systemctl status <service>
systemctl start <service>
systemctl stop <service>
systemctl restart <service>
systemctl enable <service>
systemctl disable <service>

journalctl -u <service>
journalctl -f
journalctl -b

systemctl --failed
```