# Network Sharing

## File Transfer with `rsync`

```bash
# Sync Files on the Same Host
rsync -avh /my/local/directory/one/ /my/local/directory/two/
# Sync Files from a Remote Host to a Local Host
rsync -avh username@remotehost.com:/remote/directory/ /local/directory/
```

- `-v`: Verbose output, showing which files are being transferred

- `-r`: Recursive, which is necessary for copying entire directories

- `-h`: Human-readable output, displaying numbers in a more understandable format (e.g., KB, MB)

- `-z`: Compresses file data during the transfer, which is great for slow connections

- `-a`: Archive mode, a convenient shortcut that combines several options (-rlptgoD) to preserve permissions, ownership, and timestamps

## Simple HTTP Server

```bash
# Move to any folder, whose content will be accessible over HTTP
cd ~/my-website
# Starting a Simple HTTP Server in Linux
python -m http.server
# Access the server
http://IP_ADDRESS:8000
```

## NFS (Network File System)

Temporary Mount

```bash
# Ensure the NFS Client Service is running
sudo service nfsclient start
# Mounting the NFS Share to your local host
sudo mount server_host_name.dvu.local:/directory /mount_directory
```

Permanent Mount

```bash
# Add an entry to "/etc/fstab"
server_host_name.dvu.local:/directory    /mount_directory    nfs    defaults    0 0
# Test the configuration without rebooting, "-a" means mount all filesystems listed in "/etc/fstab" that aren't already mounted
sudo mount -a
```

## Samba and SMB

SMB is the `protocol`. Samba is `software` that implements `SMB` on Unix/Linux systems

SMB helps to share files between OS (Linux-Windows)

```bash
# Install the Samba package
sudo apt update
sudo apt install samba
# Open the configuration file in a text editor
sudo nano /etc/samba/smb.conf
# Restart the Samba service
sudo service smbd restart
# At the bottom of the file, add a new section for your share
[myshare]
    comment = My First Samba Share
    path = /my/directory/to/share
    read only = no
    browsable = yes
# Create the directory you specified in the configuration
mkdir -p /my/directory/to/share
# Create password for Samba access
sudo smbpasswd -a [username]
# Mount a Samba Share
sudo mount -t cifs //SERVER/sharename /mnt/mountpoint -o user=username,pass=password
```