# Linux Commands Cheat Sheet
## STORAGE
### Basic Commands


| Command                        | Description               |
|--------------------------------|---------------------------|
| `lsblk`                        | List all block devices    |
| `sudo fdisk --list /dev/sda`   | List of partitions on a block device |
| `sudo cfdisk /dev/sdb` | List of partitions on a block device (If you don't pass any blockdevice input, it defaults to /dev/sda) |
| `swapon --show`  | To see if a system uses any swap memory/space  |
| `sudo mkswap /dev/sdb3 AND  sudo swapon --verbose /dev/sdb3` | To formate a partition as swap |
| `sudo swapoff /dev/sdb3` | To stop using a partition as a swap space |



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
