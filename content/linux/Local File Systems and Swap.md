
# Local File Systems and Swap

## Topics
- File system benefits, categories, and types
- Ext3/Ext4, XFS, VFAT, and ISO9660
- File system administration commandset
- Mount and unmount file systems manually and persistently
- Determine and use UUID
- Apply and use file system label
- Monitor file system and directory usage
- Create and mount different types of local file systems in partitions
- Create, mount, and resize Ext4 and XFS file systems in LVM
- Understand, create, and activate swap in partitions and LVM

#### RHCSA Objectives:


30\. Configure systems to mount file systems at boot by Universally
Unique ID (UUID) or label

31\. Add new partitions and logical volumes, and swap to a system
nondestructively (the first part of this objective is covered in more
detail in Chapter 13 )

32\. Create, mount, unmount, and use vfat, ext4, and xfs file systems

35\. Extend existing logical volumes (more details in Chapter 13 also)


File systems 

- can be optimized, resized, mounted, and unmounted independently.
- must be connected to the directory hierarchy in order to be accessed by users and applications. This may be accomplished automatically at system boot or manually as required. File systems 
- can be mounted or unmounted using their unique identifiers, labels, or device files. \

swap space. Swapping 
- provides a mechanism to move out and in pages of idle data between physical memory and swap. 
- Swap areas act as extensions to the physical memory, and they 
- may be activated or deactivated independent of swap spaces located in other partitions and volumes.

## File Systems and File System Types 

- Each file system is created in a discrete partition, VDO volume, or logical volume. 
- A typical production RHEL system usually has numerous file systems. 
- During OS installation, only two file systems---/ and /boot---are created in the default disk layout, but you can design a custom disk layout and construct separate containers to store dissimilar
information. 
- Typical additional file systems that may be created during an installation are /home, /opt, /tmp, /usr, and /var. 
- The two mandatory file systems---/ and /boot---are required for installation and booting.

Storing disparate data in distinct file systems versus storing all data in a single file system offers the following advantages:

- Make any file system accessible (mount) or inaccessible (unmount) to users independent of other file systems. This hides or reveals information contained in that file system.
- Perform file system repair activities on individual file systems
- Keep dissimilar data in separate file systems
- Optimize or tune each file system independently
- Grow or shrink a file system independent of other file systems

3 types of file system: disk-based, network-based, and memory-based.

**Disk-based**
- typically created on physical drives using SATA, USB, Fibre Channel, and other technologies. 
 
**Network-based** 
- essentially disk-based file systems shared over the network for remote access. 
- 
**Memory-based** 
- virtual
- created at system startup and destroyed when the system goes down.

Disk-based and network-based file systems store information persistently, while any data saved in virtual file systems does not
survive across system reboots.


  Ext3
    -  Disk                   
    -  The third generation of the extended filesystem. It supports metadata journaling for faster recovery, offer superior reliability, allows the creation of up to 32,000 subdirectories, and supports larger file systems and bigger files than its predecessor.

  Ext4
    - Disk
    - successor to Ext3. It 
    - supports all features of Ext3 in addition to:
	    - a larger file system size, 
	    - bigger file size, an 
	    - unlimited number of subdirectories, 
	    - metadata and quota journaling, and 
	    - extended user attributes.

  XFS                     
    - Disk 
    - XFS is a highly scalable and high-performing 64-bit file system. It
    - supports:
	    - metadata journaling for faster crash recovery, and 
	    - online defragmentation, expansion, quota journaling, and extended user attributes. XFS is the 
	- default file system type in RHEL 9.

  VFAT                    
  - Disk                 
  - used for post-Windows 95 file system formats on hard disks, USB drives, and floppy disks.

  ISO9660                 
  - Disk                    This is 
  - used for optical file systems such as CD and DVD.

  NFS - (Network File System.)          
  - Network                
  - shared directory or file system for remote access by other Linux systems.

  AutoFS  (Auto File System)               
  - Network 
  - NFS file system set to mount and unmount automatically on remote client systems.

## Extended File Systems

- first generation is obsolete and is no longer supported
- second, third, and fourth generations are currently available and supported. 
- fourth generation is the latest in the series and is superior in features and enhancements to its predecessors.
- structure is built on a partition or logical volume at the time of file system creation. This structure is divided into two sets: 
	- **first set** holds the file system's **metadata** and it is very tiny. 
		- includes superblock
			- keeps vital file system structural information:
				- type
				- size
				- status of the file system
				- number of data blocks it contains
				- automatically replicated and maintained at various known locations throughout the file system. 
				- primary superblock
					- superblock at the beginning of the file system 
				- backup superblocks. 
					- I used to supplant the corrupted or lost primary superblock to bring the file system back to its normal state.
					- Copy of the primary
		- contains the inode table
			- maintains a list of index node (inode) numbers. 
			- Each file is assigned an **inode number** at the time of its creation, and the inode number
				- holds the file's attributes such as:
					- type, 
					- permissions, 
					- ownership, 
					- owning group, 
					- size
					- last access/modification time
					- holds and keeps track of the pointers to the actual data blocks where the file contents are located.
	- **second set** stores the actual data, and it occupies almost the entire partition or the logical volume (VDO and LVM) space.\

**journaling** 
- Supported by Ext3 and Ext4
- provides the ability to recover swiftly after a system crash.
- keep track of recent changes in their metadata in a journal (or log). 
- Each metadata update is written in its entirety to the journal after completion. 
- The system peruses the journal of each extended file system following the reboot after a crash to determine if there are any errors, 
- Lets the system recover the file system rapidly using the latest metadata information stored in its journal.

- Ext3 that supports file systems up to 16TiB and files up to 2TiB, 
- Ext4 supports very large file systems up to 1EiB (ExbiByte) and files up to 16TiB (TebiByte).
	- uses a series of contiguous physical blocks on the hard disk called extents, resulting in improved read and write performance with reduced fragmentation. 
	- supports extended user attributes, metadata and quota journaling, etc.

## XFS File System

- high-performing 64-bit extent-based journaling file system type. XFS 
- allows the creation of file systems and files up to 8EiB (ExbiByte). It 
- does not run file system checks at system boot; rather, it 
- relies on you to use the xfs_repair utility to manually fix any issues. XFS 
- sets the extended user attributes and certain mount options by default on new file systems. It 
- enables defragmentation on mounted and active file systems to keep as much data in contiguous blocks as possible for faster access. 
- inability to shrink.
- uses journaling for metadata operations, guaranteeing the consistency of the file system against abnormal or forced unmounting. The
- journal information is read and any pending metadata transactions are replayed when the XFS file system is remounted.
- speedy input/output performance. It 
- can be snapshot in a mounted, active state.

## VFAT File System 

- extension to the legacy FAT file system (FAT16)
- supports 255 characters in filenames including spaces and periods; however, it still  
- does not differentiate between lowercase and uppercase letters. 
- primarily used on removable media, such as floppy and USB flash drives, for exchanging data between Linux and Windows.

## ISO9660 File System

- used for removable optical disc media such as CD/DVD drives

## File System Management 


### File System Administration Commands 

- Some are limited to their operations on the Extended, XFS, or VFAT file system type, while  
- others are general and applicable to all file system types. 


#### Extended File System                

e2label                             
  - Modifies the label of a file system

tune2fs                             
  - Tunes or displays file system attributes

#### XFS                                 

xfs_admin                           
 - Tunes file system attributes

xfs_growfs                          
  - Extends the size of a file system

xfs_info                            
- Exhibits information about a file system

#### VFAT                                

#### General File System Commands        

blkid                               
  - Displays block device attributes including their UUIDs and labels

df                                  
  - Reports file system utilization

du                                  
- Calculates disk usage of directories and file systems

fsadm                               
- Resizes a file system. This command is automatically invoked when the lvresize command is run with the -r switch.

lsblk                               
- Lists block devices and file systems and their attributes including their UUIDs and labels

mkfs                                
- Creates a file system. Use the -t option and specify ext3, ext4, vfat, or xfs file system type.

mount                               
- Mounts a file system for user access. Displays currently mounted file systems.

umount                              
- Unmounts a file system

### Mounting and Unmounting File Systems 

- file system must be connected to the directory structure at a desired attachment point, (mount point) 
- A mount point in essence is any empty directory that is created and used for this purpose.

use the mount command to view information about mounted file systems. The following shows the
XFS file systems only:
```
[root@server2 ~]# mount -t xfs
/dev/mapper/rhel-root on / type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
/dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
```

#### Mount command
-t option 
- type. 

- used for mounting a file system to a mount point
- performed with the root user privileges.
- requires the absolute pathnames of the file system block device and the mount point name. 
- accepts the UUID or label of the file system in lieu of the block device name. 
- mount all or a specific type of file system. 
- Upon successful mount, the kernel places an entry for the file system in the /proc/self/mounts file.
- A mount point should be empty when an attempt is made to mount a file system on it, otherwise the content of the mount point will hide.
- the mount point must not be in use or the mount attempt will fail.

auto (noauto)                       
- Mounts (does not mount) the file system when the -a option is specified

defaults                            
- Mounts a file system with all the default values (async, auto, rw, etc.)

`_netdev`                         
- Used for a file system that requires network connectivity in place before it can be mounted. NFS is an example.

remount                             
- Remounts an already mounted file system to enable or disable an option

ro (rw)                             
- Mounts a file system read-only read/write)

#### umount Command
- used to detach a file system from the directory hierarchy and make it inaccessible to users and applications. 
- expects the absolute pathname to the block device containing the file system or its mount point name in order to detach it. 
- umount to unmount all or a specific type of file system. 
- kernel removes the corresponding file system entry from the /proc/self/mounts file after it has been successfully disconnected.
## Determining the UUID of a File System 

- Every Extended and XFS file system has a 128-bit (32 hexadecimal characters) UUID (Universally Unique IDentifier) assigned to it at the time of its creation. In contrast, 
- UUIDs assigned to vfat file systems are 32-bit (8 hexadecimal characters) in length. 
- Assigning a UUID makes the file system unique among many other file systems that potentially exist on the system. 
- persistent across system reboots. 
- used by default in RHEL 9 in the /etc/fstab file for any file system that is created by the system in a standard partition.

- RHEL attempts to mount all file systems listed in the /etc/fstab file at reboots. 
- Each file system has an associated device file and UUID, but may or may not have a corresponding label. 
- The system checks for the presence of each file system's device file, UUID, or label, and then attempts to mount it.

Determine the UUID of /boot
```
[root@server2 ~]# lsblk | grep boot
├─sda1          8:1    0    1G  0 part /boot
```
```
[root@server2 ~]# sudo xfs_admin -u /dev/sda1
UUID = 630568e1-608f-4603-9b97-e27f82c7d4b4

[root@server2 ~]# sudo blkid /dev/sda1
/dev/sda1: UUID="630568e1-608f-4603-9b97-e27f82c7d4b4" TYPE="xfs" PARTUUID="7dcb43e4-01"

[root@server2 ~]# sudo lsblk -f /dev/sda1
NAME FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
sda1 xfs                630568e1-608f-4603-9b97-e27f82c7d4b4  616.1M    36% /boot
```

For extended file systems, you can use the `tune2fs`, `blkid`, or `lsblk` commands to determine the UUID.

A UUID is also assigned to a file system that is created in a VDO or LVM volume; however, it need not be used in the fstab file, as the device
files associated with the logical volumes are always unique and persistent.

## Labeling a File System 

- A unique label may be used instead of a UUID to keep the file system association with its device file exclusive and persistent across system reboots. 
- A label is limited to a maximum of 12 characters on the XFS file system 
- 16 characters on the Extended file system. 
- By default, no labels are assigned to a file system at the time of its creation.

The /boot file system is located in the /dev/sda1 partition and its type is XFS. You can use the xfs_admin or the lsblk command as follows to
determine its label:
```
[root@server2 ~]# sudo xfs_admin -l /dev/sda1
label = ""

[root@server2 ~]# sudo lsblk -f /dev/sda1
NAME FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
sda1 xfs                630568e1-608f-4603-9b97-e27f82c7d4b4  616.1M    36% /boot
```

- not needed on a file system if you intend to use its UUID or if it is created in a logical volume
- you can still apply one using the `xfs_admin` command with the `-L` option. 
- Labeling an XFS file system requires that the target file system be unmounted.

unmount /boot, set the label "bootfs" on its device file, and remount it:
```
[root@server2 ~]# sudo umount /boot
[root@server2 ~]# sudo xfs_admin -L bootfs /dev/sda1
writing all SBs
new label = "bootfs"

```

confirm the new label by executing `sudo xfs_admin -l /dev/sda1` or `sudo lsblk -f /dev/sda1`.

For extended file systems, you can use the `e2label` command to apply a label and the `tune2fs`, `blkid`, and `lsblk` commands to view and verify.

Now you can replace the `UUID=\"22d05484-6ae1-4ef8-a37d-abab674a5e35`\" for /boot in the fstab file with `LABEL=bootfs`, and unmount and remount /boot as demonstrated above for confirmation.
```
[root@server2 ~]# mount /boot
mount: (hint) your fstab has been modified, but systemd still uses
       the old version; use 'systemctl daemon-reload' to reload.
```

A label may also be applied to a file system created in a logical volume; however, it is not recommended for use in the fstab file, as the
device files for logical volumes are always unique and remain persistent across system reboots.

## Automatically Mounting a File System at Reboots 

## /etc/fstab
- File systems defined in the /etc/fstab file are mounted automatically at reboots. 
- must contain proper and complete information for each listed file system. 
- An incomplete or inaccurate entry might leave the system in an undesirable or unbootable state. 
- only need to specify one of the four attributes---block device name, UUID, label, or mount point---of the file system that you wish to mount manually with the mount command. 
- The mount command obtains the rest of the information from this file. 
- only need to specify one of these attributes with the umount command to detach it from the directory hierarchy.
- contains entries for file systems that are created at the time of installation. 

```
[root@server2 ~]# cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Sun Feb 25 12:11:47 2024
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel-root   /                       xfs     defaults        0 0
LABEL=bootfs /boot                   xfs     defaults        0 0
/dev/mapper/rhel-swap   none                    swap    defaults        0 0
```

EXAM TIP: Any missing or invalid entry in this file may render the system unbootable. You will have to boot the system in emergency mode to
fix this file. Ensure that you understand each field in the file for both file system and swap entries.

The format of this file is such that each row is broken out into six columns to identify the required attributes for each file system to be
successfully mounted. Here is what the columns contain:

Column 1: 
- physical or virtual device path where the file system is resident, or its associated UUID or label. 
- can be entries for network file systems here as well.

Column 2: 
- Identifies the mount point for the file system. 
- swap partitions, use either "none" or "swap".

Column 3: 
- type of file system such as Ext3, Ext4, XFS, VFAT, or ISO9660. 
- For swap, the type "swap" is used.  
- may use "auto" instead to leave it up to the mount command to determine the type of the file system.

Column 4: 
- Identifies one or more comma-separated options to be used when mounting the file system. 
- consult the manual pages of the mount command or the fstab file for additional options and details.

Column 5: 
- used by the dump utility to ascertain the file systems that need to be dumped. 
- value of 0 (or the absence of this column) disables this check. 
- This field is applicable only on Extended file systems; 
- XFS does not use it.

Column 6: 
- sequence number in which to run the e2fsck (file system check and repair utility for Extended file system types) utility on the file system at system boot. 
- By default, 0 is used for memory-based, remote, and removable file systems, 1 for /, and 2 for /boot and other physical file systems. 0 can also be used for /, /boot, and other physical file systems you don't want to be checked or repaired. 
- applicable only on Extended file systems; 
- XFS does not use it.

- 0 in columns 5 and 6 for XFS, virtual, remote, and removable file system types has no meaning. You do not need to add them for these file system types.

## Monitoring File System Usage 

### df (disk free) command 
- reports usage details for mounted file systems.
- reports the numbers in KBs unless the -m or -h option is specified to view the sizes in MBs or human-readable format.

Let's run this command with the -h option on server2:
```
[root@server2 ~]# df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs               4.0M     0  4.0M   0% /dev
tmpfs                  888M     0  888M   0% /dev/shm
tmpfs                  356M  5.1M  351M   2% /run
/dev/mapper/rhel-root   17G  2.0G   15G  12% /
tmpfs                  178M     0  178M   0% /run/user/0
/dev/sda1              960M  344M  617M  36% /boot

```

Column 1:
- file system device file or type

Columns 2, 3, 4, 5, 6
- total, used, and available spaces in and the usage percentage and mount point

Useful flags

-T 
- add the file system type to the output (example: df -hT)

-x 
- exclude the specified file system type from the output (example: df -hx tmpfs)

-t 
- limit the output to a specific file system type (example: df -t xfs)

-i
- show inode information (example: df -hi)

## Calculating Disk Usage 

### du command
- reports the amount of space a file or directory occupies. 
- -m or -h option to view the output in MBs or human-readable format. In addition, you can 
- view a usage summary with the -s switch and a grand total with -c.

Run this command on the /usr/bin directory to view the usage summary:
```
[root@server2 ~]# du -sh /usr/bin
151M	/usr/bin

```

Add a "total" row to the output and with numbers displayed in KBs:
```
[root@server2 ~]# du -sc /usr/bin
154444	/usr/bin
154444	total
```

```
[root@server2 ~]# du -sch /usr/bin
151M	/usr/bin
151M	total
```

Try this command with different options on the /usr/sbin/lvm file and observe the results.

## Exercise 14-1: Create and Mount Ext4, VFAT, and XFS File Systems in Partitions (server2)

- create 2 x 100MB partitions on the /dev/sdb disk, 
- initialize them separately with the Ext4 and VFAT file system types, 
- define them for persistence using their UUIDs, 
- create mount points called /ext4fs1 and /vfatfs1, 
- attach them to the directorystructure
- verify their availability and usage
- you will use the disk /dev/sdc and repeat the above procedure to establish an XFS file system in it and mount it on /xfsfs1.

1\. Apply the label "msdos" to the sdb disk using the parted command:
```
[root@server20 ~]# sudo parted /dev/sdb mklabel msdos
Warning: The existing disk label on /dev/sdb will be destroyed and all data on this disk will be
lost. Do you want to continue?
Yes/No? y                                                                 
Information: You may need to update /etc/fstab.

```

2\. Create 2 x 100MB primary partitions on sdb with the parted command:
```
[root@server20 ~]# sudo parted /dev/sdb mkpart primary 1 101m
Information: You may need to update /etc/fstab.

[root@server20 ~]# sudo parted /dev/sdb mkpart primary 102 201m
Information: You may need to update /etc/fstab.

```

3\. Initialize the first partition (sdb1) with Ext4 file system type using the mkfs command:
```
[root@server20 ~]# sudo mkfs -t ext4 /dev/sdb1
mke2fs 1.46.5 (30-Dec-2021)
/dev/sdb1 contains a LVM2_member file system
Proceed anyway? (y,N) y
Creating filesystem with 97280 1k blocks and 24288 inodes
Filesystem UUID: 73db0582-7183-42aa-951d-2f48b7712597
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done 
```

4\. Initialize the second partition (sdb2) with VFAT file system type using the mkfs command:
```
[root@server20 ~]# sudo mkfs -t vfat /dev/sdb2
mkfs.fat 4.2 (2021-01-31)
```

5\. Initialize the whole disk (sdc) with the XFS file system type using the mkfs.xfs command. Add the -f flag to force the removal of any old partitioning or labeling information from the disk.
```
[root@server20 ~]# sudo mkfs.xfs /dev/sdc -f 
Filesystem should be larger than 300MB.
Log size should be at least 64MB.
Support for filesystems like this one is deprecated and they will not be supported in future releases.
meta-data=/dev/sdc               isize=512    agcount=4, agsize=16000 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=64000, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=1368, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

6\. Determine the UUIDs for all three file systems using the lsblk command:
```
[root@server2 ~]# lsblk -f /dev/sdb /dev/sdc
NAME   FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
sdb                                                                           
├─sdb1 ext4   1.0         0bdd22d0-db53-40bb-8cc7-36efc9184196                
└─sdb2 vfat   FAT16       FB3A-6572                                           
sdc    xfs                91884326-9686-4569-96fa-9adb02c1f6f4>)
```

7\. Open the /etc/fstab file, go to the end of the file, and append entries for the file systems for persistence using their UUIDs:
```
UUID=0bdd22d0-db53-40bb-8cc7-36efc9184196 /ext4fs1 ext4 defaults 0 0                
UUID=FB3A-6572 /vfatfs1 vfat defaults 0 0                                          
UUID=91884326-9686-4569-96fa-9adb02c1f6f4 /xfsfs1 xfs defaults 0 0
```

8\. Create mount points /ext4fs1, /vfatfs1, and /xfsfs1 for the three
file systems using the mkdir command:
`[root@server2 ~]# sudo mkdir /ext4fs1 /vfatfs1 /xfsfs1`

9\. Mount the new file systems using the mount command. This command will fail if there are any invalid or missing information in the file.
```
[root@server2 ~]# sudo mount -a
mount: (hint) your fstab has been modified, but systemd still uses
       the old version; use 'systemctl daemon-reload' to reload.
```

10\. View the mount and availability status as well as the types of all three file systems using the df command:
```
[root@server2 ~]# df -hT
Filesystem            Type      Size  Used Avail Use% Mounted on
devtmpfs              devtmpfs  4.0M     0  4.0M   0% /dev
tmpfs                 tmpfs     888M     0  888M   0% /dev/shm
tmpfs                 tmpfs     356M  5.1M  351M   2% /run
/dev/mapper/rhel-root xfs        17G  2.0G   15G  12% /
/dev/sda1             xfs       960M  344M  617M  36% /boot
tmpfs                 tmpfs     178M     0  178M   0% /run/user/0
/dev/sdb1             ext4       84M   14K   77M   1% /ext4fs1
/dev/sdb2             vfat       95M     0   95M   0% /vfatfs1
/dev/sdc              xfs       245M   15M  231M   6% /xfsfs1
```

## Exercise 14-2: Create and Mount Ext4 and XFS File Systems in LVM Logical Volumes (server2)


- create a volume group called vgfs comprised of a 172MB physical volume created in a partition on the /dev/sdd disk.
- The PE size for the volume group should be set at 16MB. You will 
- create two logical volumes called ext4vol and xfsvol of sizes 80MB each and initialize them with the Ext4 and XFS file system types.
- ensure that both file systems are persistently defined using their logical volume device filenames. 
- create mount points called /ext4fs2 and /xfsfs2, 
- mount the file systems, and 
- verify their availability and usage.

1\. Create a 172MB partition on the sdd disk using the parted command:
![](image-ZNAS86YQ.jpg)



2\. Initialize the sdd1 partition for use in LVM using the pvcreate
command:

\



![](image-XTVY1S8C.jpg)



3\. Create the volume group vgfs with a PE size of 16MB using the
physical volume sdd1:

\



![](image-W6V3KOJT.jpg)



\

The PE size is not easy to alter after a volume group creation, so
ensure it is defined as required at creation.

\

4\. Create two logical volumes ext4vol and xfsvol of size 80MB each in
vgfs using the lvcreate command:

\



![](image-TYHEPQ9I.jpg)



5\. Format the ext4vol logical volume with the Ext4 file system type
using the mkfs.ext4 command:

\



![](image-J0ADC230.jpg)



You may alternatively use sudo mkfs -t ext4 /dev/vgfs/ext4vol.

\

6\. Format the xfsvol logical volume with the XFS file system type using
the mkfs.xfs command:

\



![](image-6NMGL0VI.jpg)



You may use sudo mkfs -t xfs /dev/vgfs/xfsvol instead.

\

7\. Open the /etc/fstab file, go to the end of the file, and append
entries for the file systems for persistence using their device files:

\



![](image-JIZ01CSU.jpg)



8\. Create mount points /ext4fs2 and /xfsfs2 using the mkdir command:

\



![](image-YCZ3J60S.jpg)



9\. Mount the new file systems using the mount command. This command
will fail if there is any invalid or missing information in the file.

\



![](image-7VJ6GFLV.jpg)



Fix any issues in the file if reported.

\

10\. View the mount and availability status as well as the types of the
new LVM file systems using the lsblk and df commands:

\



![](image-YGHO41IZ.jpg)



The lsblk command output illustrates the LVM logical volumes (ext4vol
and xfsvol), the disk they are located on (sdd), the sizes (80MB), and
the mount points (/ext4fs2 and /xfsfs2) where the file system are
connected to the directory structure.

\

The df command shows the size and usage information. Both file systems
are added to the fstab file for persistence, meaning future system
reboots will remount them automatically. They may now be used to store
files.

 




## Exercise 14-3: Resize Ext4 and XFS File Systems in LVM Logical Volumes 

\

This exercise should be done on server2 as user1 with sudo where
required.

\

In this exercise, you will grow the size of the vgfs volume group that
was created in Exercise 14-2 by adding the whole sde disk to it. You
will extend the ext4vol logical volume along with the file system it
contains by 40MB using two separate commands. You will extend the xfsvol
logical volume along with the file system it contains by 40MB using a
single command. You will verify the new extensions.

\

1\. Initialize the sde disk and add it to the vgfs volume group:

\



![](image-N1S4ZEQ8.jpg)



2\. Confirm the new size of vgfs using the vgs and vgdisplay commands:

\



![](image-HUYFRPBH.jpg)



\



![](image-M47AUSQL.jpg)



There are now two physical volumes in the volume group and the total
size increased to 400MiB.

\

3\. Grow the logical volume ext4vol and the file system it holds by 40MB
using the lvextend and fsadm command pair. Make sure to use an uppercase
L to specify the size. The default unit is MiB. The plus sign (+)
signifies an addition to the current size.

\



![](image-EIAUIGSP.jpg)



The resize subcommand instructs the fsadm command to grow the file
system to the full length of the specified logical volume.

\

4\. Grow the logical volume xfsvol and the file system (-r) it holds by
(+) 40MB using the lvresize command:

\



![](image-ZWXJ43LJ.jpg)



5\. Verify the new extensions to both logical volumes using the lvs
command. You may also issue the lvdisplay or vgdisplay command instead.

\



![](image-RV52MD1F.jpg)



6\. Check the new sizes and the current mount status for both file
systems using the df and lsblk commands:

\



![](image-O1J4S4SQ.jpg)



The outputs reflect the new sizes (128MB) for both file systems. They
also indicate their mount status.

\

This concludes the exercise.

 




## Exercise 14-4: Create and Mount XFS File System in LVM VDO Volume 

\

This exercise should be done on server2 as user1 with sudo where
required.

\

In this exercise, you will create an LVM VDO volume called lvvdo1 of
virtual size 20GB on the 5GB sdf disk in a volume group called vgvdo1.
You will initialize the volume with the XFS file system type, define it
for persistence using its device files, create a mount point called
/xfsvdo1, attach it to the directory structure, and verify its
availability and usage.

\

1\. Initialize the sdf disk using the pvcreate command:

\



![](image-RIKR613A.jpg)



2\. Create vgvdo1 volume group using the vgcreate command:

\



![](image-CJ3HB6FP.jpg)



3\. Display basic information about the volume group:

\



![](image-HBLFK52R.jpg)



The volume group contains a total of 1279 PEs.

\

4\. Create a VDO volume called lvvdo1 using the lvcreate command. Use
the -l option to specify the number of logical extents (1279) to be
allocated and the -V option for the amount of virtual space (20GB).

\



![](image-2XDC7EWH.jpg)



Confirm with a y when prompted to wipe old signatures.

\

5\. Display detailed information about the volume group including the
logical volume and the physical volume:

\



![](image-NU4QU06N.jpg)



\



![](image-G4EYBOGA.jpg)



The output reflects the creation of two logical volumes: a pool called
/dev/vgvdo1/vpool0 and a volume called /dev/vgvdo1/lvvdo1.

\

6\. Display the new VDO volume creation using the lsblk command:

\



![](image-SVVL6JGY.jpg)



The output shows the virtual volume size (20GB) and the underlying disk
size (5GB).

\

7\. Initialize the VDO volume with the XFS file system type using the
mkfs.xfs command. The VDO volume device file is
/dev/mapper/vgvdo1-lvvdo1 as indicated in the above output. Add the -f
flag to force the removal of any old partitioning or labeling
information from the disk.

\



![](image-XHACPUN9.jpg)



8\. Open the /etc/fstab file, go to the end of the file, and append the
following entry for the file system for persistent mounts using its
device file:

\



![](image-AU8TDGE5.jpg)



9\. Create the mount point /xfsvdo1 using the mkdir command:

\



![](image-U6XSRERT.jpg)



10\. Mount the new file system using the mount command. This command
will fail if there are any invalid or missing information in the file.

\



![](image-K8DOB3ZN.jpg)



The mount command with the -a flag is a validation test for the fstab
file. It should always be executed after updating this file and before
rebooting the server to avoid landing the system in an unbootable state.

\

11\. View the mount and availability status as well as the type of the
VDO file system using the lsblk and df commands:

\



![](image-WVWQLFHC.jpg)



The lsblk command output illustrates the VDO volume name (lvvdo1), the
disk it is located on (sdf), the actual size (5GB) and the virtual size
(20GB), and the mount point (/xfsvdo1) where the file system is
connected to the directory structure.

\

The df command shows the logical size of the file system that users will
see, but it does not reveal the underlying disk information. This file
system is added to the fstab file for persistence, meaning a future
system reboot will remount it automatically. This file system may now be
used to store files.

\

Refer to Chapter 13 "Storage Management" for details on VDO.

 




## Swap and its Management 

\

Physical memory (or main memory) in the system is a finite temporary
storage resource employed for loading kernel and running user programs
and applications. Swap space is an independent region on the physical
disk used for holding idle data until it is needed. The system splits
the physical memory into small logical chunks called pages and maps
their physical locations to virtual locations on the swap to facilitate
access by system processors. This physical-to-virtual mapping of pages
is stored in a data structure called page table, and it is maintained by
the kernel.

\

When a program or process is spawned, it requires space in the physical
memory to run and be processed. Although many programs can run
concurrently, the physical memory cannot hold all of them at once. The
kernel monitors the memory usage. As long as the free memory remains
above a high threshold, nothing happens. However, when the free memory
falls below that threshold, the system starts moving selected idle pages
of data from physical memory to the swap space to make room to
accommodate other programs. This piece in the process is referred to as
page out. Since the system CPU performs the process execution in a
round-robin fashion, when the system needs this paged-out data for
execution, the CPU looks for that data in the physical memory and a page
fault occurs, resulting in moving the pages back to the physical memory
from the swap. This return of data to the physical memory is referred to
as page in. The entire process of paging data out and in is known as
demand paging.

\

RHEL systems with less physical memory but high memory requirements can
become over busy with paging out and in. When this happens, they do not
have enough cycles to carry out other useful tasks, resulting in
degraded system performance. The excessive amount of paging that affects
the system performance is called thrashing.

\

When thrashing begins, or when the free physical memory falls below a
low threshold, the system deactivates idle processes and prevents new
processes from being launched. The idle processes are only reactivated,
and new processes are only allowed to be started when the system
discovers that the available physical memory has climbed above the
threshold level and thrashing has ceased.

 




## Determining Current Swap Usage 

\

The size of a swap area should not be less than the amount of physical
memory; however, depending on workload requirements, it may be twice the
size or larger. It is also not uncommon to see systems with less swap
than the actual amount of physical memory. This is especially witnessed
on systems with a huge physical memory size.

\

RHEL offers the free command to view memory and swap space utilization.
Use this command to view how much physical memory is installed (total),
used (used), available (free), used by shared library routines (shared),
holding data before it is written to disk (buffers), and used to store
frequently accessed data (cached) on the system. The -h flag may be
specified with the command to list the values in human-readable format,
otherwise -k for KB, -m for MB, -g for GB, and so on are also supported.
Add -t with the command to display a line with the "total" at the bottom
of the output. Here is a sample output from server2:

\



![](image-X5OW4QON.jpg)



The output indicates that the system has 1.7GiB of total memory of which
1.0GiB is in use and 443MiB is free. It also shows on the same line the
current memory usages by temporary (tmpfs) file systems (11MiB) and
kernel buffers and page cache (430MiB). It also illustrates an estimate
of free memory available to start new processes (702MiB).

\

On the subsequent row, it reports the total swap space (2.0GiB)
configured on the system with a look at used (0 Bytes) and free (2.0GiB)
space. The last line prints the combined utilization of both main memory
and swap.

\

Try free -hts 3 and free -htc 2 to refresh the output every three
seconds (-s) and to display the output twice (-c).

\

The free command reads memory and swap information from the
/proc/meminfo file to produce the report. The values are shown in KBs by
default, and they are slightly off from what is shown in the above
screenshot with free. Here are the relevant fields from this file:

\



![](image-V1DJ6BRY.jpg)



This data depicts the usage of the system's runtime memory and swap.

 




## Prioritizing Swap Spaces 

\

On many production RHEL servers, you may find multiple swap areas
configured and activated to meet the workload demand. The default
behavior of RHEL is to use the first activated swap area and move on to
the next when the first one is exhausted. The system allows us to
prioritize one area over the other by adding the option "pri" to the
swap entries in the fstab file. This flag supports a value between -2
and 32767 with -2 being the default. A higher value of "pri" sets a
higher priority for the corresponding swap region. For swap areas with
an identical priority, the system alternates between them.

 




## Swap Administration Commands 

\

In order to create and manage swap spaces on the system, the mkswap,
swapon, and swapoff commands are available. Use mkswap to initialize a
partition for use as a swap space. Once the swap area is ready, you can
activate or deactivate it from the command line with the help of the
other two commands, or set it up for automatic activation by placing an
entry in the fstab file. The fstab file accepts the swap area's device
file, UUID, or label.

 




## Exercise 14-5: Create and Activate Swap in Partition and Logical Volume 

\

This exercise should be done on server2 as user1 with sudo where
required.

\

In this exercise, you will create one swap area in a new 40MB partition
called sdb3 using the mkswap command. You will create another swap area
in a 140MB logical volume called swapvol in vgfs. You will add their
entries to the /etc/fstab file for persistence. You will use the UUID
and priority 1 for the partition swap and the device file and priority 2
for the logical volume swap. You will activate them and use appropriate
tools to validate the activation.

\

 {style="border-style: solid; border-color: rgb(0, 0, 0); border-top-width: 1px; width: 0.0625;"}


\

EXAM TIP: Use the lsblk command to determine available disk space.

\

 {style="border-style: solid; border-color: rgb(0, 0, 0); border-top-width: 1px; width: 0.0625;"}


\

1\. Use parted print on the sdb disk and the vgs command on the vgfs
volume group to determine available space for a new 40MB partition and a
144MB logical volume:

\



![](image-479W71MV.jpg)



The outputs show 49MB (250MB minus 201MB) free space on the sdb disk and
144MB free space in the volume group.

\

2\. Create a partition called sdb3 of size 40MB using the parted
command:

\



![](image-ALDICG61.jpg)



3\. Create logical volume swapvol of size 144MB in vgs using the
lvcreate command:

\



![](image-7U0JHKDJ.jpg)



4\. Construct swap structures in sdb3 and swapvol using the mkswap
command:

\



![](image-VL5QVXNY.jpg)



5\. Edit the fstab file and add entries for both swap areas for
auto-activation on reboots. Obtain the UUID for partition swap with
lsblk -f /dev/sdb3 and use the device file for logical volume. Specify
their priorities.

\



![](image-W8FIIC8U.jpg)



\

 {style="border-style: solid; border-color: rgb(0, 0, 0); border-top-width: 1px; width: 0.0625;"}


\

EXAM TIP: You will not be given any credit for this work if you forget
to add entries to the fstab file.

\

 {style="border-style: solid; border-color: rgb(0, 0, 0); border-top-width: 1px; width: 0.0625;"}


\

6\. Determine the current amount of swap space on the system using the
swapon command:

\



![](image-WLC5BSTD.jpg)



There is one 2GB swap area on the system and it is configured at the
default priority of -2.

\

7\. Activate the new swap regions using the swapon command:

\



![](image-S9UJB0TV.jpg)



The command would display errors if there are any issues with swap
entries in the fstab file.

\

8\. Confirm the activation using the swapon command or by viewing the
/proc/swaps file:

\



![](image-IGWVFJ1X.jpg)



The activation of the two new swap regions is confirmed from the above
outputs. Their sizes and priorities are also visible. The device mapper
device files for the logical volumes and the device file for the
partition swap are also exhibited.

\

9\. Issue the free command to view the reflection of swap numbers on the
Swap and Total lines:

\



![](image-31DA1VNU.jpg)



The total swap is now 2.2GiB. This concludes the exercise.

 




## Chapter Summary 

\

This chapter covered two major storage topics: file systems and swap.
These structures are created in partitions or VDO/logical volumes
irrespective of the underlying storage management solution used to build
them.

\

The chapter began with a detailed look at the concepts, categories,
benefits, and types of file systems. We reviewed file system
administration and monitoring utilities. We discussed the concepts
around mounting and unmounting file systems. We examined the UUID
associated with file systems and applied labels to file systems. We
analyzed the file system table and added entries for auto-activating
file systems at reboots. We explored tools for reporting file system
usage and calculating disk usage. We performed a number of exercises on
file system creation and administration in partitions and VDO and LVM
volumes to reinforce the concepts and theory learned in this and the
previous chapters.

\

We touched upon the concepts of swapping and paging, and studied how
they work. We performed exercises on creating, activating, viewing,
deactivating, and removing swap spaces, as well as configuring them for
auto-activation at system reboots.

 




## Review Questions 

\

1\. What type of information does the blkid command display?

2\. What would the command xfs_admin -L bootfs /dev/sda1 do?

3\. The lsblk command cannot be used to view file system UUIDs. True or
False?

4\. Which two file systems are created in a default RHEL 9 installation?

5\. What would the command lvresize -r -L +30 /dev/vg02/lvol2 do?

6\. XFS is the default file system type in RHEL 9. True or False?

7\. What is the process of paging out and paging in known as?

8\. What would the command mkswap /dev/sdc2 do?

9\. What would happen if you tried to mount a file system on a directory
that already contains files in it?

10\. A UUID is always assigned to a file system at its creation time.
True or False?

11\. Arrange the activities to create and activate a swap space while
ensuring persistence: (a) swapon, (b) update fstab, (c) mkswap, and (d)
reboot?

12\. The difference between the primary and backup superblocks is that
the primary superblock includes pointers to the data blocks where the
actual file contents are stored whereas the backup superblocks don't.
True or False?

13\. What would the command mkfs.ext4 /dev/vgtest/lvoltest do?

14\. Arrange the tasks in correct sequence: umount file system, mount
file system, create file system, remove file system.

15\. Which of these statements is wrong with respect to file systems:
(a) optimize each file system independently, (b) keep dissimilar data in
separate file systems, (c) grow and shrink a file system independent of
other file systems, and (d) file systems cannot be expanded independent
of other file systems.

16\. Which command can be used to create a label for an XFS file system?

17\. Which virtual file contains information about the current swap?

18\. The /etc/fstab file can be used to activate swap spaces
automatically at system reboots. True or False?

19\. What is the default file system type used for optical media?

20\. The xfs_repair command must be run on a mounted file system. True
or False?

21\. Provide two commands that can be used to activate and deactivate
swap spaces manually.

22\. Provide the fstab file entry for an Ext4 file system located in
device /dev/mapper/vg20-lv1 and mounted with default options on the
/ora1 directory.

23\. What is the name of the virtual file that holds currently mounted
file system information?

24\. Both Ext3 and Ext4 file system types support journaling. True or
False?

25\. What would the mount command do with the -a switch?

26\. What would the command df -t xfs do?

27\. Which command can be used to determine the total and used physical
memory and swap in the system?

28\. Name three commands that can be employed to view the UUID of an XFS
file system?

 




## Answers to Review Questions 

\

1\. The blkid command displays attributes for block devices.

2\. The command provided will apply the specified label to the XFS file
system in /dev/sda1.

3\. False.

4\. / and /boot.

5\. The command provided will expand the logical volume lvol2 in volume
group vg02 along with the file system it contains by 30MB.

6\. True.

7\. The process of paging out and in is known as demand paging.

8\. The command provided will create swap structures in the /dev/vdc2
partition.

9\. The files in the directory will hide.

10\. True.

11\. c/a/b or c/b/a.

12\. False.

13\. The command provided will format /dev/vgtest/lvoltest logical
volume with Ext4 file system type.

14\. Create, mount, unmount, and remove.

15\. d is incorrect.

16\. The xfs_admin command can be used to create a label for an XFS file
system.

17\. The /proc/swaps file contains information about the current swap.

18\. True.

19\. The default file system type for optical devices is ISO9660.

20\. False.

21\. The swapon and swapoff commands.

22\. /dev/mapper/vg20-lv1 /ora1 ext4 defaults 0 0

23\. The mounts file under /proc/self directory.

24\. True.

25\. The command provided will mount all file systems listed in the
/etc/fstab file but are not currently mounted.

26\. The command provided will display all mounted file systems of type
XFS.

27\. The free command.

28\. You can use the xfs_admin, lsblk, and blkid commands to view the
UUID of an XFS file system.

 




## Do-It-Yourself Challenge Labs 

\

The following labs are useful to strengthen most of the concepts and
topics learned in this chapter. It is expected that you perform the labs
without external help. A step-by-step guide is not supplied, as the
knowledge and skill required to implement the labs have already been
disseminated in the chapter; however, hints to the relevant major
topic(s) are included.

\

Use the lab environment built specifically for end-of-chapter labs. See
sub-section "Lab Environment for End-of-Chapter Labs" in Chapter 01
"Local Installation" for details.

 




## Lab 14-1: Create VFAT, Ext4, and XFS File Systems in Partitions and Mount Persistently 

\

As user1 with sudo on server4, create three 70MB primary partitions on
one of the available 250MB disks (lsblk) by invoking the parted utility
directly at the command prompt. Apply label "msdos" if the disk is new.
Initialize partition 1 with VFAT, partition 2 with Ext4, and partition 3
with XFS file system types. Create mount points /vfatfs5, /ext4fs5, and
/xfsfs5, and mount all three manually. Determine the UUIDs for the three
file systems, and add them to the fstab file. Unmount all three file
systems manually, and execute mount -a to mount them all. Run df -h for
verification. (Hint: File System Management).

 




Lab 14-2: Create XFS File System in LVM VDO Volume and Mount
Persistently

\

As user1 with sudo on server4, ensure that VDO software is installed.
Create a volume vdo5 with a logical size 20GB on a 5GB disk (lsblk)
using the lvcreate command. Initialize the volume with XFS file system
type. Create mount point /vdofs5, and mount it manually. Unmount the
file system manually and execute mount -a to mount it back. Run df -h to
confirm. (Hint: File System Management).

 




## Lab 14-3: Create Ext4 and XFS File Systems in LVM Volumes and Mount Persistently 

\

As user1 with sudo on server4, initialize an available 250MB disk for
use in LVM (lsblk). Create volume group vg200 with PE size 8MB and add
the physical volume. Create two logical volumes lv200 and lv300 of sizes
120MB and 100MB. Use the vgs, pvs, lvs, and vgdisplay commands for
verification. Initialize the volumes with Ext4 and XFS file system
types. Create mount points /lvmfs5 and /lvmfs6, and mount them manually.
Add the file system information to the fstab file using their device
files. Unmount the file systems manually, and execute mount -a to mount
them back. Run df -h to confirm. (Hint: File System Management).

 




## Lab 14-4: Extend Ext4 and XFS File Systems in LVM Volumes 

\

As user1 with sudo on server4, initialize an available 250MB disk for
use in LVM (lsblk). Add the new physical volume to volume group vg200.
Expand logical volumes lv200 and lv300 along with the underlying file
systems to 200MB and 250MB. Use the vgs, pvs, lvs, vgdisplay, and df
commands for verification. (Hint: File System Management).

 




## Lab 14-5: Create Swap in Partition and LVM Volume and Activate Persistently 

\

As user1 with sudo on server4, create two 100MB partitions on an
available 250MB disk (lsblk) by invoking the parted utility directly at
the command prompt. Apply label "msdos" if the disk is new. Initialize
one of the partitions with swap structures. Apply label swappart to the
swap partition, and add it to the fstab file. Execute swapon -a to
activate it. Run swapon -s to confirm activation.

\

Initialize the other partition for use in LVM. Expand volume group vg200
(Lab 14-3) by adding this physical volume to it. Create logical volume
swapvol of size 180MB. Use the vgs, pvs, lvs, and vgdisplay commands for
verification. Initialize the logical volume for swap. Add an entry to
the fstab file for the new swap area using its device file. Execute
swapon -a to activate it. Run swapon - s to confirm activation. (Hint:
Swap and its Management).

 



