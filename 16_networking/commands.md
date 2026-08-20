rsync

```bash
# Sync Files on the Same Host
rsync -avh /my/local/directory/one/ /my/local/directory/two/
# Sync Files from a Remote Host to a Local Host
rsync -avh username@remotehost.com:/remote/directory/ /local/directory/
```

http server

```bash
# Move to any folder, whose content will be accessible over HTTP
cd ~/my-website
# Starting a Simple HTTP Server in Linux
python -m http.server
# Access the server
http://IP_ADDRESS:8000
```

NFS

```bash
# Temporary Mount
sudo service nfsclient start
sudo mount server_host_name.dvu.local:/directory /mount_directory
```

```bash
# Permanent Mount: add an entry to "/etc/fstab"
server_host_name.dvu.local:/directory    /mount_directory    nfs    defaults    0 0
sudo mount -a
```

Samba/SMB

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