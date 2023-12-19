# RHEL Study Guide: Manage Basic Storage  
[RHEL Study Guide - Table of Contents](https://github.com/pslucas0212/RHEL-Study-Guide)  

Identify unused disks using lsblk command.  lsblk lists information about all available or the specified block devices. The lsblk command reads the sysfs filesystem and udev db to gather information. The command prints all block devices (except RAM disks) in a tree-like format by default.
```
# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
vda    252:0    0   10G  0 disk
├─vda1 252:1    0    1M  0 part
├─vda2 252:2    0  200M  0 part /boot/efi
├─vda3 252:3    0  500M  0 part /boot
└─vda4 252:4    0  9.3G  0 part /
vdb    252:16   0    5G  0 disk
vdc    252:32   0    5G  0 disk
vdd    252:48   0    5G  0 disk
```

Use parted to create and manage disk partitions. Check disks for partions
```
# parted /dev/vdb print
Error: /dev/vdb: unrecognised disk label
...
```

Make a partion table or map or partitioning scheme.
```
# parted /dev/vdb mklabel gpt
Information: You may need to update /etc/fstab.
```

Create a partion.
```
# parted /dev/vdb mkpart backup xfs 2048s 2GB
Information: You may need to update /etc/fstab.
```

Confirm partion creationg
```
# parted /dev/vdb print
Model: Virtio Block Device (virtblk)
...
```

Run udevadm settle to make sure the "new" device is detected by the system
```
# udevadm settle
```

Format the file system, mount the new file systems
```
# mkfs.xfs /dev/vdb1
meta-data=/dev/vdb1              isize=512    agcount=4, agsize=121984 blks
...
# mkdir /mount
# mount 
```

