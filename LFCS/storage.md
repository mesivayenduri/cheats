# Linux Commands Cheat Sheet
## STORAGE
### Basic Commands


| Command                        | Description               |
|--------------------------------|---------------------------|
| `lsblk`                        | List all block devices    |
| `sudo fdisk --list /dev/sda`   | List of partitions on a block device |
| `sudo cfdisk /dev/sdb`        | List of partitions on a block device (If you don't pass any blockdevice input, it defaults to /dev/sda) |
| `swapon --show`  | To see if a system uses any swap memory/space  |
| `sudo mkswap /dev/sdb3`   | To make or format /dev/sdb3 partition as swap |
| `sudo swapon --verbose /dev/sdb3` | To start using /dev/sdb3 partition as swap |
| `sudo swapoff /dev/sdb3`  | To stop using a partition as a swap space |
| `sudo mkfs.xfs /dev/sdb1`     |  To format/create a partition as a xfs file system |
| `sudo mkfs.ext4 /dev/sdb1`    | To format/create a partition as a ext4 file system |
| `sudo tune2fs -L "Backup Volume" /dev/sdb1` | Tune2fs is the utility which can help us to adjust filesystem parameters  |
| `sudo tune2fs -l /dev/sdb1` |  To check the parameters of a partitiion |
| `sudo mount /dev/sdb1 /mnt/` | To mount the partition /dev/sdb1 to /mnt path (This is will not survive reboots) - To survive reboots check mount section |
| `sudo umount /mnt` | To unmount the filesystem |
| `sudo blkid /dev/sdb1` | To check the UUID of a partition |
| `ls -l /dev/disk/by-uuid`  | To check UUID of all partitions  |
| `findmnt`  | To show all mountpoints in the filesystem. Tt shows everything including lvms, etc |
| `findmnt -t xfs,ext4` |  To show only real filesystems like ext4 or xfs mounted on this system |
| `sudo mount -o ro /dev/sdb1 /mnt`| To mount a filesystem as ReadOnly |
| `sudo mount -o rw,noexec,nosuid /dev/sdb1 /mnt`  | To mount a filesystem with rw, noexec ( noexec: makes it impossible to launch a program that is stored in the filesystem ) and nosuid ( nosuid: disables suid permision that can allow programs to run with root permission without needing sudo commad)   |
| `sudo mount -o remount,ro /dev/sdb1 /mnt` | To remount the filesystem without needing to unmount and mount  |
| `sudo blkid /dev/sdb1`  | To check UUID of a partition  |



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



# REMOTE FILE SYSTEMS : NFS
Setting up Network file system involes 2 things.
    1. Setting up NFS Server
    2. Setting up NFS Client

## On the NFS Server side:
```bash
    sudo apt install nfs-kernel-server
```
    -> After installing above required binaries we need to tell our server what filesystem/directories you want to share.
    -> This can be done by simply mentioning the details in /etc/exports file

```bash
vim /etc/exports
/etc 127.0.0.1(ro)
```

### After Updating the above file we need to export it using below command
```bash
sudo exportfs -r  ( r means re-export)
```

### To list the exported NFS
```bash
sudo exportfs -v
```

## On the NFS Client side: We need to install below.
```bash
sudo apt install nfs-common
```

### To mount an NFS volume on clientside
```bash
sudo mount <IPAddress/CIDR/Hostname>:<Path of dir exported on nfs> <Path to whcih we need to mount nfs dir>
sudo mount 127.0.0.1:/etc /mnt
```

### To unmount
```bash
umount /mnt
```

### Whatever the changes we did above doens't survive reboots. To mount them at boot time
```bash
vim /etc/fstab

127.0.0.1:/etc /mnt nfs defaults    0 0
```
