# thinkpad-x1-yoga-with-fedora
Installing Fedora on a Thinkpad X1 Yoga laptop
==============================================

The Thinkpad X1 Yoga Gen4 is being delivered with a 512GB harddisk and Windows 11 preinstalled.

This article shows how to set up Fedora on top of this in dual-boot fashion, keeping 70GB for Windows and using the rest for Fedora.

The original partition schema of the X1 Yoga looks like this:
```
[root@localhost-live ~]# parted /dev/nvme0n1
GNU Parted 3.6
Using /dev/nvme0n1
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) p                                                                
Model: KXG6AZNV512G TOSHIBA (nvme)
Disk /dev/nvme0n1: 512GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End    Size    File system  Name                          Flags
 1      1049kB  274MB  273MB   fat32        EFI system partition          boot, hidden, esp, no_automount
 2      274MB   290MB  16.8MB               Microsoft reserved partition  msftres, no_automount
 3      290MB   511GB  511GB   ntfs         Basic data partition          msftdata
 4      511GB   512GB  1049MB  ntfs         Basic data partition          hidden, diag, no_automount
```

We need to leave partition 1, 2 and 4 untouched, they serve for booting the laptop (part 1+2) and for recovering Windows (part 4). We are going to shrink partition 3 and let Fedora installer add two partitions in tht free space.

Step 1 - Boot Fedora live system for installation
-------------------------------------------------
Download the latest Fedora release of your choice (I use KDE as my graphical interface, so it is https://fedoraproject.org/spins/kde/download where I get the ISO from) and write it to a USB stick.
With the USB stick inserted, power up the laptop and keep F12 key pressed until the system shows the boot options screen; in case Windows boots, you need to wait until it has finished, go through initial setup procedure and then reboot to keep the filesystem clean for the following repartitioning exercises.

Step 2 - Resize partition
-------------------------
Having booted into the Fedora live system, open a terminal using the Konsole application and become root by entering
```
sudo su -
```

Check if the partition layout is as shown above
```
parted -l
```

Now enter
```
ntfsresize -s 60G /dev/nvme0n1p3
```
to resize the Windows partition to 60GB; you can check with
```
parted -l
```
or
```
fdisk -l /dev/nvme0n1
```

Reboot with
```
reboot
```
and again keep F12 pressed to make sure you boot into Fedora's live system for installation.
