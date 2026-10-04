# Commands

```bash
$ lsblk -o NAME,SERIAL,SIZE,TYPE
NAME    SERIAL       SIZE TYPE
sda     ZHZ1A2BC    16.4T disk

$ dmesg -T | tail -8
[Tue Sep 15 03:11:02 2026] sd 1:0:6:0: [sdg] tag#412 FAILED Result: hostbyte=DID_OK driverbyte=DRIVER_SENSE
[Tue Sep 15 03:11:02 2026] sd 1:0:6:0: [sdg] tag#412 Sense Key : Medium Error [current]

$ sudo smartctl -a /dev/sdg
SMART overall-health self-assessment test result: FAILED!
Serial Number:    ZHZ4K8NT
Power_On_Hours:   68914
ID# ATTRIBUTE_NAME          VALUE WORST THRESH TYPE      RAW_VALUE
  1 Raw_Read_Error_Rate      061   044   006   Pre-fail  138204216
  5 Reallocated_Sector_Ct    071   071   010   Pre-fail  2384
  7 Seek_Error_Rate          089   060   045   Pre-fail  812449
187 Reported_Uncorrect       001   001   000   Old_age   411
197 Current_Pending_Sector   100   100   000   Old_age   176
198 Offline_Uncorrectable    100   100   000   Old_age   176

$ multipath -ll
mpatha (35000c500e1a4b101) dm-0 SEAGATE,ST16000NM001G
size=14T features='0' hwhandler='1 alua' wp=rw
`-+- policy='service-time 0' prio=50 status=active
  |- 1:0:0:0 sda 8:0   active ready running
  `- 2:0:0:0 sdm 8:192 active ready running

$ df -h /var
Filesystem             Size  Used Avail Use% Mounted on
/dev/mapper/vg0-var     40G   40G     0 100% /var

$ du -sh /var
12G     /var

$ sudo lsof +L1
COMMAND   PID  USER   FD   TYPE DEVICE SIZE/OFF NLINK NAME
rsyslogd 1184 root    7w   REG  253,0 30000000000  0 /var/log/application.log (deleted)
```