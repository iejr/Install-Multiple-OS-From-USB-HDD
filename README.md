# Install OS from USB Hard Drive  

![Alt test]( https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/Preview.jpg )  

### Content:
#### 1. What is this special disk
#### 2. How to configure the disk
#### 3. Preparation Step
#### 4. Boot Windows Installation
#### 5. Boot WinPE Environment
#### 6. Boot Ubuntu Installation
#### 7. Boot CentOS Installation

### Main Body:

#### 1. What is this special disk?
Nowadays perhaps everybody knows the most popular way to install Windows 7 OS, which may be using a usb flash disk. However, what if one want to reinstall OS every 2 month? For a geek, he may try every OS he like, in this case he has a lot to do with his usb flash disk. Even if for a normal person, each time before he prepares his usb flash disk, he must be sure to backup all data in the usb disk in case the flashing process will erase them all.

What you need is a smart usb hard drive. It is so large today that you could put plenty of OS installations files in it. With a little configuration, there it is. When you want to install a new OS, just connect it into PC, then reboot the PC from the usb hard drive. A menu will be displayed, choose the OS you want, and the next it should be the same process as a normal setup.


#### 2. How to configure the disk?
The configuration is easy. Let's take Windows 7 as an example.  
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
There are many ways to do. I take the default Disk tools in Ubuntu as an example.  

2. Create a new primary partition with small space such as 64MB, type fat32.  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/2-GrubPart.png)  
This partiton used for storage Grub4dos files. Download Grub4dos from its official website, and put them in the partition.( If what you've download is a package, you need to extract them first ) Now, the root directory of this partition looks like this:  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/3-GrubDir.png)  
3. Configure the MBR as Windows NT. If the MBR is Windows NT, then at the time the disk is booting by the bios, the first active partition will be used on the disk. There is a powerful tools named BOOTICE which may be useful. I use the version 1.3. This software can modify MBR and PBR, which are important parts of our DIY phase. Could not find the English version, sorry for that, and I used Chinese version like this:  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/4-ModifyMBR.png)  

4. The next step is to set the partition with grub files as an active partition:  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/5-ActivePartition.png)

5. Configure the PBR of this partition as Grub4Dos. We still use BOOTICE:  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/6-ModifyPBR.png)  
The PBR type Grub4Dos will search and boot the file named "GRLDR" in the root directory of the partiton if it set to Grub4Dos. Then, GRLDR will find menu.lst file to complete the booting phase, and the menu.lst file is the way to customize our booting.  

6. Create a new external partition.
This partitions is used to storage the OS files. The space is up to you and the total space of the disk. You could use all space left. For me, however, I have a 160GB disk. And I part 90GB for this partition. Because I want some extra space so that the disk can be used as a normal usb HDD. Thus, the last 60GB is a normal NTFS partition to storage some other files in my life.   
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/7-ExtendPartition.png)  

All Preparations are done. In the following step we assume these steps have been finished.

#### 4. Boot Windows 7/8/10 Installation
Booting into windows installations is easy. Just put the installations files in a new partition and add a new entry to the menu.lst. Let's take windows 10 as an example(Win7 and Win8 are the same). Here is the process:  
1. Create a new partition in the extend partiton of the usb HDD. The space should be at least 4.1GB. The type is fat32.  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/8-WinPartition.png)

2. Download a win10 Installation iso file, extract all of them into the root of the new partition.

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

Now save all your work, then reboot your computer and to check if all changes has taken effect.  

#### 5. Boot WinPE Environment
Windows PE would be a powerful tools if one usually custom a lot on disks. In grub4dos, to boot into WinPE environment is not difficult.  
1. Create a new Fat32 paritions on the extend part of the usb HDD. The space requirement could be at least 1GB, I think it can hold most of the PE nowadays.  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/9-WinPEPartition.png)  
2. Download a PE iso file, and put it into the new partition directly. Assume the iso file is "WinPE.iso".
3. Modifiy the menu.lst file

  ```
  timeout 10
  default 0

  find --set-root /GRUB/FONTS.GZ
  fontfile /GRUB/FONTS.GZ   

  title [01]----Enter WinPE Environment
    find --set-root /WinPE.iso
    map --mem /WinPE2003.iso (0xff)
    map --hook        
    chainloader (0xff)
  ```    

  In this case we use command "map". It could map the iso file to the RAM so the chainloader could boot from the RAM. The "--hook" argument means to take map opeartion works immediately. However, if you ask me why is 0xff, I could explain either since I failed to find the anwser. Anyone knows reason could we have a discussion.  

All step are done, reboot to check the result.  


#### 6. Boot Ubuntu Installation
Ubuntu is a popular linux distribution now. In this tutorial I will take a 64 bit Ubuntu 14.04 as example. Booting linux is a little complex since the kernel is usually need extra argument.  
1. Create a new FAT32 partition with 1GB space.  
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/10-UbuntuPartition.png)  
2. Download ubuntu 14.04 iso from its official website.
  >http://www.ubuntu.com/desktop

  Extract all files in the iso to the new partition. Create a new identity empty file named "BootingUbuntu14" in the same location.  

3. Modify the menu.lst.

  ```
  timeout 10
  default 0

  find --set-root /GRUB/FONTS.GZ
  fontfile /GRUB/FONTS.GZ
  title [01]----Install Ubuntu 14.04 amd64 Desktop System
    find --set-root /BootingUbuntu14
    kernel /casper/vmlinuz.efi file=/preseed/ubuntu.seed boot=casper initrd=/casper/initrd.lz ignore_uuid live-media-path=/casper quiet splash
    initrd /casper/initrd.lz
  ```

  There are two new syntax used, kernel and initrd. In general, to boot into a linux OS, kernel is loaded first, then goes initrd. More details in linux startup process could be found here:
  >https://en.wikipedia.org/wiki/Linux_startup_process

  In the menu.lst example, we assign the kernel file as /casper/vmlinuz.efi, where the root is the parition which has a file named "BootingUbuntu14". Find the casper directory of your ubuntu partition, there should be a file named like "vmlinuz", please change the /casper/vmlinuz.efi to the real file name. Then goes a lot of argument like "boot=casper",etc. They are all what the kernel need, and actually I don't know the meaning of them all.  
  Then, we assign initrd file as /casper/initrd.lz. Same to the kernel, the name should be match to the one in the ubuntu partition. Same to the kernel, initrd also has a lot of argument, and I found this all on the forum of ubuntu.

END
#### 7. Boot CentOS Installation
As a popular personal server system, CentOS is widely used in our life, or not for normal guys. But, when the idea that you determine to build a PC server comes to you someday, you may remember this article for easily install it.  
For instance, I'll use CentOS 7, which is the newest version of CentOS family now.
1. Create a new FAT32 partition with 4GB space on the USB HDD's extend partition.
![Alt test](https://github.com/iejr/Install-OS-From-USB-HDD/blob/master/src/image/11-CentOSPartition.png)
2. Download CentOS 7 image from
>https://www.centos.org/

  Extract all files to the new partition, with a new file "BootingCentOS7".
3. Edit the menu.lst

  ```
  timeout 10
  default 0

  find --set-root /GRUB/FONTS.GZ
  fontfile /GRUB/FONTS.GZ

  title [01]----Install CentOS 7 x86_64 System
    find --set-root /BootingCentOS7
    kernel /isolinux/vmlinuz inst.stage2=hd:LABEL=CENTOS7X64 quiet rd.live.check
    initrd /isolinux/initrd.img
    boot
  ```

  The only thing different from others is to care about the kernel argument "inst.stage2=hd:LABEL=CENTOS7X64", we need to change the centos partition' label to "CENTOS7X64", or the kernel couldn't find the right partition later.

END
