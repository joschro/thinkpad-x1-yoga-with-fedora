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
ntfsresize -s 65G /dev/nvme0n1p3
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

Step 3 - Install Fedora
-----------------------
Back in the Fedora istallation live system, click the "Install to Hard Drive" icon on the screen twice and go through the language setup dialogue.

![grafik](https://github.com/joschro/thinkpad-x1-yoga-with-fedora/assets/12337748/5dd9304a-face-41f6-9b39-5a28942cf5aa)


Select "Custom" for storage configuration
![grafik](https://github.com/joschro/thinkpad-x1-yoga-with-fedora/assets/12337748/f306b7bf-9766-4145-b6d5-932374122bca)


Check "Encrypt my data" and click "Click here to create them automatically"
![grafik](https://github.com/joschro/thinkpad-x1-yoga-with-fedora/assets/12337748/6771b5fe-296c-4ac6-aa6f-c4d205fa3e58)

It should look like this:
![grafik](https://github.com/joschro/thinkpad-x1-yoga-with-fedora/assets/12337748/29fd0ef6-a6c8-43c7-b776-9b6da2a3c987)

In case you wanted to install additional operating systems or add another partition for other purposes, you could change the size of the Linux partition:
![grafik](https://github.com/joschro/thinkpad-x1-yoga-with-fedora/assets/12337748/7f0bc39a-b32c-4d09-9c6e-e57c2e1715a9)

Click "Done" and enter the encryption passphrase:
![grafik](https://github.com/joschro/thinkpad-x1-yoga-with-fedora/assets/12337748/066937b6-e83a-45da-8c2e-7357e97d6fb5)

After this final step, you'll see a summary of changes to be made on the disk; confirm and proceed with the remaining configuration steps like adding a user, timezone settings etc.

Step 4 - Using Windows
----------------------
You should start Windows using the F12 boot selection dialogue on start-up; this way Windows won't ask for a BitLocker recovery key. Otherwise, you could boot Windows by selecting the entry in the Grub boot loader, but you would have to provide the BitLocker recovery key (can be looked up at https://aka.ms/myrecoverykey) each time the Grub configuration gets updated, e.g. when a new kernel update has been installed.
