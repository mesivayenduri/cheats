# LVM - LOGICAL VOLUME MANAGER


### Install LVM Untilities
```bash
sudo apt install lvm2
```

### LVM Major has 4 Components
-> PV ( Physical Volume Eg: SSD)
-> VG ( Volume Groups )
-> LV ( Logical Volumes )
-> PE ( Physical Extents )

### To Examine PV's we can use
```bash
sudo lvmdiskscan
```

### To add new disks as Physical Volumes to LVM
```bash
sudo pvcreate /dev/sdc
sudo pvcreate /dev/sdc /dev/sdd
```

### To List all PVs
```bash
sudo pvs
```

As we have created the PV, next we need to tell LVM to us the storage capacity.
To do so, we need to add PVs to VG

### To create VG
```bash
sudo vgcreate <volume-name> <partitions>
sudo vgcreate mylvm /dev/sdc /dev/sdd
```

### To extend the VG, we need to add more PVs
```bash
sudo pvcreate /dev/sde
sudo vgextent mylvm /dev/sde
```

### To list all VGs
```bash
sudo vgs
```

### To remove PV from VG
```bash
sudo vgreduce mylvm /dev/sde
```
NOTE: We can only remove it if LVM didn't store any data on it yet.

### To remove the PV from LVM
```bash
sudo pvremove /dev/sde
```

Now that we have PV and VG created, we need to create partitions ( Logical Volumes (LV))

### To create a LV or Partition
```bash
sudo lvcreate --size <size> --name <lv name> <vg Name>
sudo lvcreate --size 2G --name partition1 mylvm
```

### To list all LV
```bash
sudo lvs
```

### To resize the LV
```bash
sudo lvresize --extents 100% VG mylvm/partition1
```

### To resize the LV back
```bash
sudo lvresize --size 2G mylvm/partition1
```

### To remove an LV from volume group 'mylvm'
```bash
sudo lvremove mylvm/partition1
```


### Queston : Create a Logical Volume (LV). It should have these properties:

1. The size of the LV should be 0.5 Gigabytes.

2. Its name should be smalldata.

3. The logical volume should be created on the volume group named volume1

```bash
sudo lvcreate --size 0.5G volume1 --name smalldata
```


### Question: Resize the Logical Volume called smalldata to 752 MB.

This logical volume resides on the volume group called volume1.

```bash
To increase
sudo lvresize -L+252M volume1/smalldata

To Decrease
sudo lvresize -L-12M volume1/smalldata
```



### To view the paths LVs
```bash
sudo lvdisplay
```

As we have partition created now, we can create the filesystem

```bash
sudo mkfs.ext4 <lv-path>
sudo mkfs.ext4 /dev/mylvm/partition1

sudo mkfs.xfs <lv-path>
sudo mkfs.xfs /dev/volume1/smalldata

```

Once the filesystem is create we cannot resize the partition using ```sudo lvresize --size``` option.
To achieve this
```bash
sudo lvresize --resizefs --size 3G mylvm/partition1
```