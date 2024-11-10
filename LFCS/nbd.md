# NETWORK BLOCK DEVICES : NBD

## NBD Server Side

### Install NDB Server utilities
```bash
sudo apt install nbd-server
```

### To Share a partition over network as NetworkBlockDevicce
```bash
sudo vim /etc/nbd-server/config

[generic]
#       user = nbd
#       group = nbd
        includedir = /etc/nbd-server/conf.d
        allowlist = true

# What follows are export definitions. You may create as much of them as
# you want, but the section header has to be unique.
[partition2]
        exportname = /dev/sdb2

sudo systemctl restart nbd-server.service
```


### Install NDB Client utilities
```bash
sudo atp install nbd-client

sudo modprobe nbd

sudo vim /etc/modules-load.d/modules.conf

Add 'nbd' without quotes and save it

sudo nbd-client 127.0.0.1 -N partition2

To mount nbd device
sudo mount /dev/nbd0 /mnt

To unmount
sudo umount /mnt

To disconnect the specified ndb device from the service
sudo nbd-client -d /dev/nbd0

To list the partitions allowed in NBD

sudo ndb-client -l 127.0.0.1
```

