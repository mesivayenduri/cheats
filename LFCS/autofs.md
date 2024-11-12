# AUTOMOUNTS NFS CONFIGURATION INSTEAD OF /etc/fstab APPROACH

### Install NFS and configure nfs_share dir
```bash
sudo apt install nfs-kerner-server

sudo vim /etc/exports
/nfs_share    127.0.0.1(rw,sync,no_subtree_chec)

sudo exportfs -r
```

### Install Autofs and configure automount
```bash
sudo apt install autofs

sudo vim /etc/auto.master
/misc      /etc/auto.misc    --timeout=300


sudo vim /etc/auto.misc
backup      -fstype=nfs4,rw,soft,intr    127.0.0.1:/nfs_share


sudo systemctl restart autofs && sudo systemctl enable autofs

# Until you access the path files wont get mounted.
ls -lah /misc/backup

# If the files were not accessed for 5mins files will be unmounted automatically

```
