# Linux Commands Cheat Sheet
## STORAGE
### Basic Commands


| Command                        | Description               |
|--------------------------------|---------------------------|
| `lsblk`                        | List all block devices    |
| `sudo fdisk --list /dev/sda`   | List of partitions on a block device    \#list &#35;list of partitions    |
| `sudo cfdisk /dev/sdb` | List of partitions on a block device |

```bash
vboxuser@lfcs-practice:~$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0    25G  0 disk 
├─sda1   8:1    0     1M  0 part 
├─sda2   8:2    0   513M  0 part /boot/efi
└─sda3   8:3    0  24.5G  0 part /var/snap/firefox/common/host-hunspell
                                 /
sdb      8:16   0    10G  0 disk 
sdc      8:32   0    10G  0 disk
```
