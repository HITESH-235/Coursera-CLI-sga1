# Question 5 – Storage Health Assessment and Documentation [4 Marks]

## Scenario
Your organization plans to deploy a new application server. Assess the storage resources and document your findings using the vi editor.

> **Note:** The `Storage_Assessment_Report.txt` file in this folder was created using the vi editor as required.

---

## Commands Executed and Outputs

### 1. Available Storage Devices

```bash
$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
zram0       253:0    0  15.4G  0 disk [SWAP]
nvme0n1     259:0    0 953.9G  0 disk
├─nvme0n1p1 259:1    0   260M  0 part
├─nvme0n1p2 259:2    0    16M  0 part
├─nvme0n1p3 259:3    0 681.9G  0 part
├─nvme0n1p4 259:4    0   1.4G  0 part
├─nvme0n1p5 259:5    0    20G  0 part
├─nvme0n1p6 259:6    0   260M  0 part
├─nvme0n1p7 259:7    0  1023M  0 part /boot
└─nvme0n1p8 259:8    0   249G  0 part /
```
**Explanation:** The system has a 953.9 GB NVMe SSD (`nvme0n1`) with 8 partitions. It's a dual-boot setup with Windows (p1–p6) and Linux (p7–p8). A 15.4G zram device provides compressed in-memory swap.

---

### 2. Mounted File Systems

```bash
$ df -hT
Filesystem     Type      Size  Used Avail Use% Mounted on
dev            devtmpfs   16G     0   16G   0% /dev
run            tmpfs      16G  8.1M   16G   1% /run
efivarfs       efivarfs  192K  133K   55K  71% /sys/firmware/efi/efivars
/dev/nvme0n1p8 ext4      245G   47G  186G  20% /
tmpfs          tmpfs      16G   43M   16G   1% /dev/shm
tmpfs          tmpfs      16G   13M   16G   1% /tmp
/dev/nvme0n1p7 vfat     1021M  137M  885M  14% /boot
tmpfs          tmpfs     3.1G  116K  3.1G   1% /run/user/1000
```
**Explanation:** The root filesystem (ext4) uses 47G of 245G (20%), which is healthy. The boot partition (vfat) is at 14%. Multiple tmpfs mounts provide temporary storage in RAM for runtime data.

---

### 3. Disk Usage Statistics

```bash
$ du -sh /home/hit235
27G     /home/hit235
```
**Explanation:** The user's home directory occupies 27G, which is about 11% of the root partition's total capacity.

```bash
$ du -sh /*
0       /bin
137M    /boot
0       /dev
9.4M    /etc
27G     /home
0       /lib
16K     /lost+found
2.2G    /opt
237M    /run
12K     /srv
12M     /tmp
9.0G    /usr
8.3G    /var
```
**Explanation:** The largest directories are /home (27G), /usr (9G), and /var (8.3G). Directories showing 0 are virtual filesystems (/proc, /sys) or symlinks to /usr (/bin, /lib, /sbin).

---

### 4. Inode Utilization

```bash
$ df -i
Filesystem       Inodes  IUsed    IFree IUse% Mounted on
dev             4001287    737  4000550    1% /dev
run             4025529   2745  4022784    1% /run
/dev/nvme0n1p8 16350336 615226 15735110    4% /
tmpfs           4025529     74  4025455    1% /dev/shm
tmpfs           1048576   1533  1047043    1% /tmp
/dev/nvme0n1p7        0      0        0     - /boot
tmpfs            805105    173   804932    1% /run/user/1000
```
**Explanation:** The root filesystem uses only 4% of available inodes (615K out of 16.3M). This means the system has ample capacity for creating new files. FAT32 partitions show 0 inodes because FAT uses a File Allocation Table, not inodes.

---

### 5. Block Device Filesystem Info

```bash
$ lsblk -f
NAME        FSTYPE LABEL   UUID                                 FSAVAIL FSUSE%
zram0       swap   zram0   e2306c2d-efa9-400e-8a6c-e33fe202a311
nvme0n1p1   vfat   SYSTEM  288A-FC5B
nvme0n1p3   ntfs   OS      B8528CB0528C7542
nvme0n1p4   ntfs           32BC90B4BC9073D7
nvme0n1p5   ntfs   RESTORE 6AE89F0FE89ED921
nvme0n1p6   vfat   MYASUS  509F-0CC0
nvme0n1p7   vfat           EA6F-05CC                            884M    13%
nvme0n1p8   ext4           475a7998-1285-4c51-8fda-2fd9f887ae34 185.5G  19%
```
**Explanation:** Shows filesystem types, labels, and UUIDs for each partition. The system uses ext4 (Linux root), vfat (boot/EFI), and ntfs (Windows partitions). UUIDs provide reliable identification for mounting.

---

### 6. Mount Points

```bash
$ mount | head -30
/dev/nvme0n1p8 on / type ext4 (rw,relatime)
/dev/nvme0n1p7 on /boot type vfat (rw,relatime,fmask=0022,dmask=0022,...)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev,inode64,usrquota)
tmpfs on /tmp type tmpfs (rw,nosuid,nodev,nr_inodes=1048576,inode64,usrquota)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
sys on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
```
**Explanation:** The `mount` command shows all filesystems with their mount options. The root uses `relatime` for performance, and tmpfs mounts include `nosuid` and `nodev` security options.

---

### 7. Vi Editor Operations

The `Storage_Assessment_Report.txt` was created using vi with the following steps:
```bash
$ vi Storage_Assessment_Report.txt
# In vi:
# Press 'i' to enter insert mode
# Type/paste the report content
# Press 'Esc' to return to normal mode
# Type ':wq' to save and quit
```
**Explanation:** The vi editor is a standard Unix text editor. Insert mode (`i`) allows typing text, and command mode (`:wq`) saves and exits. Vi is available on virtually all Unix/Linux systems, making it essential for system administrators.

---

## Storage Health Summary

| Metric | Value | Status |
|--------|-------|--------|
| Root disk usage | 20% (47G / 245G) | ✅ Healthy |
| Boot partition | 14% (137M / 1021M) | ✅ Healthy |
| Inode usage | 4% (615K / 16.3M) | ✅ Healthy |
| Home directory | 27G | ⚠️ Monitor |
| EFI variables | 71% | ⚠️ Monitor |
| Swap (zram) | 15.4G compressed | ✅ Healthy |

---

## Recommendations

1. **Clean package caches** to reclaim space from outdated cached packages.
2. **Monitor /var** (8.3G) — implement log rotation to control log file growth.
3. **Audit /home** (27G) — review for large unused files and old downloads.
4. **Consider separate /home partition** for data protection during OS reinstalls.
5. **Enable fstrim** for the NVMe SSD to maintain write performance and longevity.
6. **Set up disk alerts** to warn when any partition exceeds 80% usage.

---

## Files in This Folder
- `Storage_Assessment_Report.txt` – Detailed storage assessment report (created with vi)
- `README.md` – This file (commands, outputs, and explanations)
