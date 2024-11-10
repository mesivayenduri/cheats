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
