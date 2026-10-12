# Linux Web Server Administration Lab

A hands-on Linux system administration project that deploys and manages a basic web server on Ubuntu Server. The lab covers user and permission management, SSH remote access, Nginx, firewall rules, log inspection, automated backups, and system monitoring.

> **Purpose:** Build practical Linux administration skills in a small, reproducible virtual lab and document the work in a GitHub repository.

## Table of Contents

- [Linux Web Server Administration Lab](#linux-web-server-administration-lab)
  - [Table of Contents](#table-of-contents)
  - [Project Overview](#project-overview)
    - [Architecture](#architecture)
  - [Learning Objectives](#learning-objectives)
  - [Prerequisites](#prerequisites)
  - [Lab Environment](#lab-environment)
  - [1. Install and Inspect Ubuntu Server](#1-install-and-inspect-ubuntu-server)
    - [Configure a hostname](#configure-a-hostname)
  - [2. Manage Users, Groups, and Permissions](#2-manage-users-groups-and-permissions)
    - [Practice tasks](#practice-tasks)
  - [3. Configure SSH Remote Access](#3-configure-ssh-remote-access)
    - [Basic SSH hardening](#basic-ssh-hardening)
  - [4. Install and Configure Nginx](#4-install-and-configure-nginx)
    - [Useful Nginx commands](#useful-nginx-commands)
  - [5. Configure the Firewall](#5-configure-the-firewall)
  - [6. Inspect System and Web Server Logs](#6-inspect-system-and-web-server-logs)
    - [systemd journal](#systemd-journal)
    - [Nginx access and error logs](#nginx-access-and-error-logs)
    - [Practice tasks](#practice-tasks-1)
  - [7. Create a Website Backup Script](#7-create-a-website-backup-script)
    - [Test restoration](#test-restoration)
  - [8. Schedule Automated Backups](#8-schedule-automated-backups)
  - [9. Monitor System Resources](#9-monitor-system-resources)
    - [CPU and processes](#cpu-and-processes)
    - [Memory](#memory)
    - [Disk space and filesystem usage](#disk-space-and-filesystem-usage)
    - [Disk activity](#disk-activity)
    - [Network sockets](#network-sockets)
    - [Practice tasks](#practice-tasks-2)
  - [Troubleshooting](#troubleshooting)
  - [Skills Practiced](#skills-practiced)
  - [Possible Future Improvements](#possible-future-improvements)
  - [Repository Structure](#repository-structure)
  - [Safety and Lab Notes](#safety-and-lab-notes)
  - [Lab Extentions](#lab-extentions)

## Project Overview

The goal is to deploy an Ubuntu Server virtual machine that hosts a simple static website. The server will be administered remotely over SSH, restrict inbound traffic with a firewall, keep useful logs, and create scheduled website backups.

### Architecture

```text
Administrator's Windows PC
          |
          | SSH (TCP/22)
          | HTTP (TCP/80)
          v
+----------------------------------+
|          Ubuntu Server VM        |
|                                  |
|  UFW firewall                    |
|  ├── SSH: TCP/22                 |
|  └── HTTP: TCP/80                |
|                                  |
|  Nginx web server                |
|          |                       |
|          v                       |
|  /var/www/html                   |
|          |                       |
|          v                       |
|  Backup script + cron            |
|                                  |
|  systemd journal + Nginx logs    |
+----------------------------------+
```

The VM's networking mode determines how the Windows host reaches the server. Use a suitable host-only or bridged network, or configure port forwarding if using NAT.

## Learning Objectives

By completing this lab, you will practice:

- Navigating the Linux filesystem and inspecting system information.
- Creating users and managing group membership.
- Setting file ownership and permissions.
- Connecting to a Linux server over SSH.
- Managing services with `systemctl`.
- Installing software with APT.
- Hosting a static website with Nginx.
- Applying basic inbound firewall rules with UFW.
- Reading system and application logs.
- Writing a Bash backup script and scheduling it with `cron`.
- Monitoring CPU, memory, disk, and process usage.
- Testing and troubleshooting a small Linux server.

## Prerequisites

- A Windows 11 computer.
- VirtualBox or another virtualization platform.
- An Ubuntu Server LTS ISO image.
- Basic familiarity with a terminal.
- Administrator access to your own lab VM.

## Lab Environment

Suggested starting resources:

| Resource | Suggested value |
|---|---|
| Operating system | Ubuntu Server LTS |
| CPU | 2 virtual CPUs |
| RAM | 4 GB |
| Disk | 30 GB |
| Web server | Nginx |
| Firewall | UFW |
| Access method | SSH |
| Website | Static HTML |

These are starting recommendations, not strict requirements. Resource usage depends on the virtualization platform and other applications running on the computer.

> **Important:** Use a VM or other disposable lab environment. Take a snapshot before making significant configuration changes.

---

## 1. Install and Inspect Ubuntu Server

Install Ubuntu Server in your virtualization platform. During installation, create an administrative account and enable OpenSSH Server if the installer offers that option. If not, install SSH in the next section.

After logging in, inspect the system.

```bash
# Show OS and kernel information
cat /etc/os-release
uname -a

# Show hostname and system information
hostnamectl

# Show network interfaces and addresses
ip -br address

# Show the routing table
ip route

# Check DNS/network connectivity
ping -c 4 ubuntu.com
```

A failed ping does not always mean the network is broken; ICMP may be blocked. If it fails, check the IP address, default route, DNS configuration, and VM network mode.

Practice filesystem navigation:

```bash
pwd
ls -lah
cd /etc
cd ~
mkdir -p ~/linux-lab
```

### Configure a hostname

Choose a hostname for your lab server:

```bash
sudo hostnamectl set-hostname linux-web-01
hostnamectl
```

**Expected result:** The VM has a recognizable hostname, a working network configuration, and can reach the package repositories.

---

## 2. Manage Users, Groups, and Permissions

Create a separate administrative account if you need one. Replace `adminuser` with your preferred username.

```bash
sudo adduser adminuser
sudo usermod -aG sudo adminuser
```

Log out and log in as that account, or start a login shell:

```bash
su - adminuser
```

Inspect identity and group membership:

```bash
whoami
id
groups
```

Create a directory for a permission exercise:

```bash
sudo mkdir -p /srv/shared-lab
sudo chown adminuser:adminuser /srv/shared-lab
touch /srv/shared-lab/test.txt
```

Set the file's permissions:

```bash
chmod 640 /srv/shared-lab/test.txt
ls -l /srv/shared-lab/test.txt
```

With mode `640`:

- Owner: read and write.
- Group: read.
- Others: no permissions.

Inspect the numeric and symbolic permission representation:

```bash
stat /srv/shared-lab/test.txt
```

### Practice tasks

1. Create a group called `webteam`.
2. Add a test user to that group.
3. Create a shared directory owned by `root:webteam`.
4. Configure directory permissions so members of `webteam` can collaborate.
5. Verify access by switching between accounts.

Use a dedicated test directory rather than experimenting on system directories.

**Expected result:** You can explain the difference between a user, a group, ownership, and read/write/execute permissions.

---

## 3. Configure SSH Remote Access

Install and start OpenSSH Server:

```bash
sudo apt update
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```

Check the service:

```bash
sudo systemctl status ssh
sudo ss -tulpn | grep ':22'
```

Find the server's IP address:

```bash
ip -br address
```

From Windows PowerShell, connect to the VM:

```powershell
ssh adminuser@SERVER_IP
```

### Basic SSH hardening

Before changing SSH settings, confirm you have a working administrative account and keep a VM console session available. If you use key-based authentication, test it in a second terminal before changing authentication settings.

Open the SSH server configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

Set or verify this directive:

```text
PermitRootLogin no
```

Validate the configuration before restarting:

```bash
sudo sshd -t
```

If the command returns no errors, reload the service:

```bash
sudo systemctl reload ssh
```

Verify that your normal administrative account can still connect before closing your existing session.

**Expected result:** You can administer the VM from Windows over SSH, and direct root login is disabled.

---

## 4. Install and Configure Nginx

Update package metadata and install Nginx:

```bash
sudo apt update
sudo apt install -y nginx
```

Enable the service and check its status:

```bash
sudo systemctl enable --now nginx
sudo systemctl status nginx
```

Create a simple website:

```bash
sudo tee /var/www/html/index.html > /dev/null <<'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Linux Administration Lab</title>
</head>
<body>
  <h1>Linux Web Server Lab</h1>
  <p>This website is hosted on Ubuntu Server using Nginx.</p>
</body>
</html>
EOF
```

Test the response locally:

```bash
curl -I http://localhost
curl http://localhost
```

Open a browser on the Windows host and visit:

```text
http://SERVER_IP
```

### Useful Nginx commands

```bash
# Check service status
sudo systemctl status nginx

# Validate Nginx configuration
sudo nginx -t

# Reload configuration after a change
sudo systemctl reload nginx

# Restart the service
sudo systemctl restart nginx
```

**Expected result:** The custom website loads in a browser, and you can manage Nginx as a systemd service.

---

## 5. Configure the Firewall

UFW is a straightforward interface for managing host firewall rules on Ubuntu.

Install it if necessary:

```bash
sudo apt install -y ufw
```

**Before enabling the firewall, allow SSH.** Otherwise, you may lock yourself out of remote administration.

```bash
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw status verbose
```

Enable UFW:

```bash
sudo ufw enable
```

Verify the rules:

```bash
sudo ufw status numbered
```

The lab should allow inbound SSH and HTTP traffic. Do not open additional ports unless a lab task needs them.

If you later configure HTTPS, you can allow HTTPS traffic with:

```bash
sudo ufw allow 443/tcp
```

**Expected result:** SSH and HTTP work from an allowed network, and UFW reports the expected rules.

---

## 6. Inspect System and Web Server Logs

Logs help you understand what happened when a service started, stopped, or encountered an error.

### systemd journal

```bash
# Show recent system messages
sudo journalctl -n 100

# Show warnings and more severe messages
sudo journalctl -p warning

# Show messages from the current boot
sudo journalctl -b

# Show Nginx service logs
sudo journalctl -u nginx --since today
```

### Nginx access and error logs

```bash
# View recent HTTP requests
sudo tail -n 50 /var/log/nginx/access.log

# View recent errors
sudo tail -n 50 /var/log/nginx/error.log

# Follow access logs live
sudo tail -f /var/log/nginx/access.log
```

While following the access log, refresh the website in your browser. Observe the new request entries, then press `Ctrl+C` to stop following the log.

### Practice tasks

1. Find a request generated by your browser.
2. Identify the HTTP status code returned for that request.
3. Find the Nginx service's recent log messages.
4. Investigate a harmless, controlled error such as requesting a nonexistent page.
5. Explain the difference between an access log and an error log.

**Expected result:** You can locate relevant logs and use them as evidence during troubleshooting.

---

## 7. Create a Website Backup Script

The script below creates a dated compressed archive of the website in a dedicated backup directory.

Create the script:

```bash
sudo nano /usr/local/sbin/backup-website.sh
```

Add:

```bash
#!/usr/bin/env bash
set -Eeuo pipefail

SOURCE_DIR="/var/www/html"
BACKUP_DIR="/var/backups/website"
DATE="$(date +%F_%H-%M-%S)"
ARCHIVE="${BACKUP_DIR}/website-${DATE}.tar.gz"

if [[ ! -d "$SOURCE_DIR" ]]; then
    echo "Error: source directory not found: $SOURCE_DIR" >&2
    exit 1
fi

install -d -m 0750 "$BACKUP_DIR"

tar -czf "$ARCHIVE" -C "$(dirname "$SOURCE_DIR")" "$(basename "$SOURCE_DIR")"

echo "Backup created: $ARCHIVE"
```

Save the file and set its permissions:

```bash
sudo chmod 750 /usr/local/sbin/backup-website.sh
```

Run it:

```bash
sudo /usr/local/sbin/backup-website.sh
```

List the archive:

```bash
sudo ls -lh /var/backups/website
```

Inspect the contents without extracting them:

```bash
sudo tar -tzf /var/backups/website/website-YYYY-MM-DD_HH-MM-SS.tar.gz
```

### Test restoration

A backup is only useful if it can be restored.

List the archive contents, then extract a selected archive into a temporary directory:

```bash
sudo mkdir -p /tmp/website-restore-test
sudo tar -xzf /var/backups/website/REPLACE_WITH_ARCHIVE_NAME.tar.gz \
  -C /tmp/website-restore-test
```

Inspect the restored files:

```bash
sudo find /tmp/website-restore-test -type f
```

Replace the archive placeholder with the actual filename. Remove the temporary test directory after verifying the contents:

```bash
sudo rm -r /tmp/website-restore-test
```

Do not test restoration by overwriting the live website. This backup script is suitable for a small lab; a production backup plan should also consider retention, off-host copies, permissions, monitoring, and recovery testing.

**Expected result:** You can create a dated backup archive, inspect it, and restore its contents to a separate test directory.

---

## 8. Schedule Automated Backups

Use cron to run the backup script daily.

Edit root's crontab:

```bash
sudo crontab -e
```

Add this line to run the script every day at 2:00 AM according to the server's local time:

```cron
0 2 * * * /usr/local/sbin/backup-website.sh >> /var/log/website-backup.log 2>&1
```

Save and exit the editor.

Verify the scheduled job:

```bash
sudo crontab -l
```

Check the server's time zone and current time:

```bash
timedatectl
date
```

For a quick test, temporarily schedule the script a few minutes in the future, then inspect the backup directory and log file. Restore the daily schedule afterward.

Check the backup log:

```bash
sudo tail -n 50 /var/log/website-backup.log
```

Cron may run with a minimal environment, so use absolute paths in scripts and test the command under the same account that owns the scheduled job.

**Expected result:** Cron runs the backup script on schedule and writes output to a log.

---

## 9. Monitor System Resources

Install basic monitoring tools:

```bash
sudo apt update
sudo apt install -y htop sysstat
```

### CPU and processes

```bash
top
htop
ps aux
```

Use `q` to exit `top` or `htop`.

### Memory

```bash
free -h
```

### Disk space and filesystem usage

```bash
df -h
du -sh /var/www/html
sudo du -sh /var/backups/website
```

### Disk activity

```bash
iostat -xz 1
```

If `iostat` is unavailable, confirm that `sysstat` installed correctly.

### Network sockets

```bash
ip -br address
ip route
ss -tulpn
```

### Practice tasks

1. Identify the process running Nginx.
2. Find the amount of available memory.
3. Determine which filesystem contains `/var/www/html`.
4. Check how much disk space the backup archives consume.
5. Identify the ports on which services are listening.

**Expected result:** You can inspect the server's resource usage and identify the basic information needed for troubleshooting.

---

## Troubleshooting

| Symptom | Checks |
|---|---|
| VM has no network connectivity | Check VM network mode, `ip -br address`, `ip route`, and DNS configuration. |
| SSH connection fails | Check the server IP, `systemctl status ssh`, listening sockets, UFW, and VM network rules. |
| Website cannot be reached | Check `systemctl status nginx`, `sudo nginx -t`, UFW, and whether port 80 is listening. |
| Nginx returns an unexpected page | Inspect `/var/www/html/index.html`, the Nginx configuration, and access logs. |
| Backup script fails | Run it manually, inspect the error, check source paths and permissions, and verify available disk space. |
| Cron job does not appear to run | Check `sudo crontab -l`, the server time zone, the script's absolute path, and the backup log. |
| Disk usage increases | Inspect `df -h`, `du -sh /var/backups/website`, and the number and age of backup archives. |

Useful general diagnostic commands:

```bash
systemctl --failed
sudo journalctl -p warning --since today
sudo ss -tulpn
df -h
free -h
```

## Skills Practiced

After completing the project, you should be able to demonstrate:

- **Linux fundamentals:** navigation, files, users, groups, and permissions.
- **Service management:** using systemd to start, stop, enable, and inspect services.
- **Networking:** IP addresses, SSH, listening ports, HTTP, and firewall rules.
- **Web administration:** installing Nginx and deploying static content.
- **Troubleshooting:** finding useful information in system and application logs.
- **Scripting:** using Bash variables, conditions, commands, and exit-on-error behavior.
- **Scheduling:** running recurring tasks with cron.
- **Monitoring:** checking resource usage and identifying basic system issues.
- **Backup and recovery:** creating archives and validating a restore.

## Possible Future Improvements

Keep the initial lab focused. Once it works reliably, consider separate follow-up projects for:

- HTTPS and TLS certificates.
- Multiple Nginx virtual hosts.
- Docker and Docker Compose deployment.
- Ansible-based server configuration and application deployment.
- Centralized logging and monitoring.
- Backup retention policies and off-host backups.

## Repository Structure

A clean GitHub repository could use this structure:

```text
linux-web-server-lab/
├── README.md
├── scripts/
│   └── backup-website.sh
└── screenshots/
    ├── nginx-homepage.png
    ├── ufw-status.png
    └── backup-archive.png
```

If you use this structure, move the backup script into `scripts/` in your repository and copy it to `/usr/local/sbin/` on the server when following the instructions. Keep screenshots free of public IP addresses, private keys, passwords, tokens, and other sensitive information.

## Safety and Lab Notes

- Run commands only on your own lab VM or systems you are authorized to administer.
- Allow SSH through the firewall before enabling it if you administer the VM remotely.
- Keep console access available while changing SSH and firewall settings.
- Use placeholders in documentation instead of publishing credentials or sensitive IP information.
- Never commit private SSH keys, passwords, API tokens, or secrets to GitHub.
- Treat this as a learning lab, not a hardened production deployment. A production service needs additional review of updates, access controls, backup retention, network exposure, monitoring, and recovery procedures.

---

**Project status:** Complete when the validation checklist passes and you can explain what each configuration does, verify that the services work, and recover from a basic failure.

## Lab Extentions

- HTTPS with Let's Encrypt
- Multiple Nginx virtual hosts
- Fail2ban for SSH protection
- Centralized logging
- Dockerized website deployment
- Monitoring with Prometheus and Grafana
- Ansible automation
