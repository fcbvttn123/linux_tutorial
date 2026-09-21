# Check RAM `free -h`

```bash
               total        used        free      shared  buff/cache   available
Mem:            15Gi       6.2Gi       2.1Gi       512Mi       7.0Gi       8.5Gi
Swap:          2.0Gi       128Mi       1.9Gi
```

- `available` already includes `free` and `buff/cache`

    - The `available` column is a calculation that takes free RAM, adds the reclaimable portion of buff/cache
    
    - And subtracts memory reserved for kernel operations

- `buff/cache` is mostly reclaimable

    - The kernel will instantly reclaim and drop memory from `buff/cache` if a running program needs it
    
    - Because of this, cached memory is functionally available for applications, even though `free` lists it as `used`

- `shared` is NOT free - it is already counted inside `buff/cache`

    - Memory in shared is actively holding data (such as files in `/tmp` or shared process memory)
    
    - It cannot be freed automatically by the kernel without terminating the process or clearing the `tmpfs` mount


# Network Troubleshooting

```bash
# verify DNS settings, check "nameserver" (nameserver 8.8.8.8)
cat /etc/resolv.conf

# test ping using IPv6
ping -6 google.com

# disable IPv6
username@debian:~$ sudo nano /etc/sysctl.conf
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv6.conf.default.disable_ipv6 = 1
    net.ipv6.conf.lo.disable_ipv6 = 1
# reload kernel parameters from a configuration file: /etc/sysctl.conf
username@debian:~$ sudo sysctl -p
```