# Install OS from USB Hard Drive  

![Alt test]( https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/Preview.jpg )  

### Content:
#### 1. What is this special disk
#### 2. How to configure the disk
#### 3. Preparation Step
#### 4. Boot Windows Installation
#### 5. Boot WinPE Environment
#### 6. Boot Linux Installation

### Main Body:

#### 1. What is this special disk?
Nowadays perhaps everybody knows the most popular way to install Windows 7 OS, which may be using a usb flash disk. However, what if one want to reinstall OS every 2 month? For a geek, he may try every OS he like, in this case he has a lot to do with his usb flash disk. Even if for a normal person, each time before he prepares his usb flash disk, he must be sure to backup all data in the usb disk in case the flashing process will erase them all.

What you need is a smart usb hard drive. It is so large today that you could put plenty of OS installations files in it. With a little configuration, there it is. When you want to install a new OS, just connect it into PC, then reboot the PC from the usb hard drive. A menu will be displayed, choose the OS you want, and the next it should be the same process as a normal setup.

Sample picture here

#### 2. How to configure the disk?
The configuration is easy. Let's take Windows 7 as an example.</br>
1. Create a new partition on the usb hard disk. Format it as fat32. The idle space should be at least 4GB( Or should be enough such that all the files in the Windows 7 Installation iso could be put into the partition ).
2. Get a Win7 Installation iso from the internet.
3. Using WinRAR or Daemon Tools to extract the files in the iso to the partition you've already made.
4. We are almost there. The last step is to let the disk know how to boot the OS. There we use a software named Grab4Dos.

Ok, the 4th step is the only one needs a little advanced trick. Grub4dos is a software that manage multiple OS Installation, more detials here:
>https://gna.org/projects/grub4dos/

In general, we actually prepare partitions for each OS, and an extra partition for Grub4dos. Then we reconfigure the grub4dos to boot mutiple OS.

#### 3. Preparation Step
Assume you have a new empty usb hard disk. There is no special requirement fot it, but it should not be smaller than 4GB or there isn't enough space for the files. Make sure there isn't any important data on the disk because we need reconfigure the partition table.  

1. Clear partition table  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/1-BlankDisk.png)  
There are many ways to do. A simple method is to using 3rd softwares.

2. Create a new primary partition with small space such as 64MB, type fat32.  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/2-GrubPart.png)  
This partiton used for storage Grub4dos files. Download Grub4dos from its official website, and put them in the partition.( If what you've download is a package, you need to extract them first ) Now, the root directory of this partition looks like this:  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/3-GrubDir.png)  
3. Configure the MBR as Windows NT. If the MBR is Windows NT, then at the time the disk is booting by the bios, the first active partition will be used on the disk. There is a powerful tools named BOOTICE which may be useful. I use the version 1.3. This software can modify MBR and PBR, which are important parts of our DIY phase. Could not find the English version, sorry for that, and I used Chinese version like this:  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/4-ModifyMBR.png)  

4. The next step is to set the partition with grub files as active partition:  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/5-ActivePartition.png)

5. Configure the PBR of this partition as Grub4Dos. We still use BOOTICE:  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/6-ModifyPBR.png)  
The PBR type Grub4Dos will search and boot the file named "GRLDR" in the root directory of the partiton if it set to Grub4Dos. Then, GRLDR will find menu.lst file to complete the booting phase, and the menu.lst file is the way to customize our booting.  

6. Create a new external partition.
This partitions is used to storage the OS files. The space is up to you and the total space of the disk. You could use all space left. However for me, I have a 160GB disk. And I part 90GB for this partition. Because I want some extra space such that the disk can be used as a normal usb HDD. Thus, the last 60GB is a normal NTFS partition to storage some other files in my life.   
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/7-ExtendPartition.png)  

All Preparations are done. In the following step we assume these steps have been finished.

#### 4. Boot Windows 7/8/10 Installation
Booting into windows installations is easy. Just put the installations files in a new partition and add a new entry to the menu.lst. Let's take windows 10 as an example.(Win7 and Win8 are the same) Here is the process:  
1. Create a new partition in the extend partiton of the usb HDD. The space should be at least 4.1GB. The type is fat32.  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/8-WinPartition.png)

2. Download an win10 Installation iso file, extract all of them into the root of the new partition.

3. Create an empty identity file named "BootingWin10" and put it into the root of the new partition.

4. Go into the grub partition, edit /grub/menu.lst  

```
timeout 10
default 0                                                              
find --set-root /GRUB/FONTS.GZ
fontfile /GRUB/FONTS.GZ

title [01]----Install Windows 10 OS
    find --set-root /BootingWin10
    chainloader /BOOTMGR
```
"title" tells grub you have a new entry.  
"----Install Windows 10 OS" is the content of this title.  
"find --set-root /BootingWin10" tells grub to change the current root directory to the partition which has a file named "BootingWin10" in it. Oh, that is what we added in the 3rd step. So the current root is set as the partition contained all windows 10 Installation files.  
"chainloader /BOOTMGR" is another syntax in the grub4dos. It means to take over control of booting to the file "BOOTMGR". And BOOTMGR is the entrance of win10 Installation phase.  

Now Save all your work, then reboot your computer and to check if all changes has taken effect.  
