# Install OS from USB Hard Drive

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
</br></br>
What you need is a smart usb hard drive. It is so large today that you could put plenty of OS installations files in it. With a little configuration, there it is. When you want to install a new OS, just connect it into PC, then reboot the PC from the usb hard drive. A menu will be displayed, choose the OS you want, and the next it should be the same process as a normal setup.
</br>
Sample picture here

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
1. Clear partition table</br>
There are many ways to do. A simple method is to using 3rd softwares.
2. Create a new main partition with small space such as 64MB, type fat32 <\br>
This partiton used for storage Grub4dos files. Download Grub4dos from its official website, and put them in the partition.( If what you've download is a package, you need to extract them first )
3. Create a new external partition. </br>
This partitions is used to storage the OS files. The space is up to you and the total space of the disk. You could use all space left. However for me, I have a 320GB disk. And I part 90GB for this partition. Because I want some extra space such that the disk can be a normal usb HDD. Thus, the last 200+GB is a normal NTFS partition to storage normall files in my life.

All Preparation are done. In the following step we assume these steps have been finished.

#### 4. Boot Windows Installation
