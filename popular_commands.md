# Bandit Game

```bash
# dash file
cat .\-

# advanced "find" command
# the "-exec file {} +" part tells find to run the "file" command on every file it discovers
find /path/to/folder -maxdepth 1 -type f -exec file {} + | grep ': ASCII text' | cut -d: -f1
find /path/to/folder -type f -user bandit7 -group bandit6 -size 33c 2> /dev/null
find /path/to/folder -type f -size 1033c ! -executable -exec file {} + | grep ': ASCII text' | cut -d: -f1

# decode a string of Base64-encoded text
base64 -d data.txt

# create a temporary workspace
cd $(mktemp -d)

# convert the hexdump back into binary
xxd -r data.txt data1

# decompress Gzip: add .gz extension and extract
mv data1 data2.gz
gzip -d data2.gz
file data2
# decompress Bzip2: extract bzip2 archive directly
bzip2 -d data2
# extract tar archive
tar -xf data3

# use the private key to SSH into localhost
ssh -i sshkey.private bandit14@localhost -p 2220

# "nc": networking utility used for reading and writing data across network connections using TCP or UDP
cat /etc/bandit_pass/bandit14 | nc localhost 30000

# "open ssl": send data over an SSL/TLS encrypted connection
openssl s_client -connect localhost:30001

# port scanning
nmap -sV -p 31000-32000 localhost

# execute the binary directly
./binary-file
```




# User and Group Management

## Core Files

- `/etc/passwd`: Stores user account details (UID, GID, home directory, shell)

- `/etc/shadow`: Stores encrypted user passwords and password expiration policies

- `/etc/group`: Stores group definitions and member lists

## User Management

```bash
# -m creates the home directory /home/alex
sudo useradd -m alex
# set password for user
sudo passwd alex
# modfy user, -s changes the user's default login shell
sudo usermod -s /bin/zsh alex
# lock user
sudo usermod -L alex
# delete user, -r removes home directory and mail spool
sudo userdel -r alex
# check which groups a user belongs to
groups username
```

## Group Management

```bash
# create group
sudo groupadd developers
# add user to group, omitting -a removes other groups
sudo usermod -aG developers alex
# drop user from the specified group
sudo gpasswd -d alex developers
# remove group (cannot delete primary group of active user)
sudo groupdel developers
# check which groups a user belongs to
groups username
```

## Auditing & Inspecting Access

```bash
# check user details: UID, primary group, and supplementary groups
id alex
# list group members
getent group developers
```

## Owner / Permission

```bash
# change owner
sudo chown alex report.txt
# change group
sudo chgrp developers report.txt
# change both owner and group
sudo chown alex:developers report.txt
# apply ownership to directory and all contents
sudo chown -R alex:developers /var/www/
# add the execute permission for the user
chmod u+x myfile
# remove permission for group
chmod g-w myfile
# modify multiple permission at once
chmod ug+w myfile
# changing permission with number: 4 (r), 2 (w), 1 (x)
chmod 755 myfile
```