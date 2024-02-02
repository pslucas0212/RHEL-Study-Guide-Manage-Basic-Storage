# RHEL Study Guide: Manage Basic Storage  
[RHEL Study Guide - Table of Contents](https://github.com/pslucas0212/RHEL-Study-Guide)  

High level steps for storage creation/management
1. lsblk - Identify unused disks with lsblk command.  lsblk list block devices connected to your system
2. parted print - Confirm a disk does not have a label with parted /dev/vdb print
3. parted mklabel - Define the partitioning scheme if needed with parted /dev/vdb mklabel gpt
4. parted mkpart - Make a partion with parted /dev/vdb mkpart backup xfs 2048s 2GB
5. udevadm settle - Make sure the sytem recognizes the partition
6. mfks.xfs - Format the partion for the file system type.  This examples uses xfs - mkfs.xfs /dev/vdb1
7. mkdir /backup  - Make a mount point if applicable
8. Get the UUID of the parttion with lsblk --fs /dev/vdb1  We need this to update fstab.  lsblk --fs or lsblk -f gives you additional detail about filesystem including the UUID
9. Update fstab - UUID=41b5e977-07b8-40dc-94ef-57de91ca2bfc /backup xfs defaults 0 0
10. systemctl daemon-reload - Force the systemctl daemon to reload to read the update fstab
11. mount /backup - Mount the backup directory
12. mount | grep /backup - Make sure /backup is mounted



High level steps for creating an additional partition on drive that has been lableled and partition
1. parted /dev/vdb print - See where the next partition becomes available
2. parted /dev/vdb mkpart swap1 linux-swap 2000M 2512M - Make next partition
3. parted /dev/vdb mkpart swap2 linux-swap 2512M 3024M - If making a third partion
4. parted /dev/vdb print and lsblk - To verify work
5. udevadm settle - Make sure the sytem recognizes the partition
6. makeswap /dev/vdb1 and mkswap /dev/vdb2 to intialize (format) swap drives
7. Update fstab - UUID=21ac624e-9d48-43b2-892a-bd5eab422ba6 swap swap pri=10 0 0
8. systemctl daemon-reload - Force the systemctl daemon to reload to read the update fstab
9. swapon -a - Activate swap
10. swapon --show - Check swap is activate

Check that the partitioning works on reboot
1. systemctl reboot
2. mount | grep /backup
3. swapon --show


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

