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