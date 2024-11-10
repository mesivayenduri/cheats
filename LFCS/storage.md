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



| Advanced File System Permissions (ACL)Commands                | Description               |
|-----------------------------------------------------|-------------------------------------|
| setfacl  | To set file access control list to a file/directory  |
| sudo apt install acl  | To install Access Control List capabilities  |
| ```sudo setfacl --modify user:jeremy:rw <filename>```  | To set rw permissions to user jeremy  |
| ```sudo getfacl filename```  | To get acls set on a file  |
| ``` sudo setfacl --modify mask:r <filename> ```  | This means that even if other ACL entries have write or execute permissions, they’ll be limited to read-only access because the mask is the "maximum allowed" permission.  |
| ``` sudo setfacl --modify group:staff:rw <filename>```  | To set rw permission to group staff  |
| ``` sudo setfacl --modify user:jeremy:-- <filename> ```  | To deny access to a user  |
| ``` sudo setfacl --remove user:jeremy <filename> ```  | To remove an acl for jeremy |
| ``` sudo setfacl --remove group:staff <filename> ```  | To remove an acl for staff group  |
| ``` sudo setfacl --remove-all <filename> ```  | To remove all acl entries  |
| ``` sudo setfacl --recursive -m user:jeremy:rwx <directory> ```  | To apply acl to a directory and all of its subdirectories and files  |
| ``` sudo setfacl --recursive --remove group:sudo <directory> ```  | To remove acl recursively on a directory and of its subdirectories and files to a gorup |
| ``` sudo setfacl --recursive --remove user:jeremy <directory> ```  | To remove acl recusively on a directory and of its subdirectories and files to a user  |
| ``` sudo chattr +a <filename> ```  | To set append attribute on a file. This will make file only appendable  |
| ``` sudo chattr -a <filename> ```  | To remove append attribute on a file  |
| ``` sudo chattr +i <filename> ```  | To set immutable attribute on a file. This will make file immutable  |
| ``` sudo chattr -i <filename> ```  | To remove immutable attribute on a file.  |
| ``` lsattr <filename> ```  | To list/show attributes of a file/directory  |



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



# REMOTE FILE SYSTEMS : 

## NFS
Visit [NFS] (https://github.com/mesivayenduri/cheats/blob/main/LFCS/nfs.md)

## NBD
Visit [NBD] (https://github.com/mesivayenduri/cheats/blob/main/LFCS/nbd.md)

## LVM
Visit [LVM] (https://github.com/mesivayenduri/cheats/blob/main/LFCS/lvm.md)