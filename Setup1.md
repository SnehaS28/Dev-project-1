# LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.

Step 1 — Prepare a Web Server

1. Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.
Learn How to Add EBS Volume to an EC2 instance here.

![5022](https://github.com/SnehaS28/Dev-project-1/blob/images/web-volumes.png)


2. Attach all three volumes one by one to your Web Server EC2 instance.

![5023](https://github.com/SnehaS28/Dev-project-1/blob/images/volume-1.png)


3. Open up the Linux terminal to begin configuration.

4. Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices
in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their 
names will likely be xvdf, xvdh, xvdg.


![5024](https://github.com/SnehaS28/Dev-project-1/blob/images/attached-vol.png)


5. Use df -h command to see all mounts and free space on your server.

![5025](https://github.com/SnehaS28/Dev-project-1/blob/images/df-h%20free%20space.png)

6. Use gdisk utility to create a single partition on each of the 3 disks.

```
sudo gdisk /dev/xvdf

```

PROMPT: 

```
 GPT fdisk (gdisk) version 1.0.3

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries.

Command (? for help branch segun-edits: p
Disk /dev/xvdf: 20971520 sectors, 10.0 GiB
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): D936A35E-CE80-41A1-B87E-54D2044D160B
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 20971486
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048        20971486   10.0 GiB    8E00  Linux LVM

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): yes
OK; writing new GUID partition table (GPT) to /dev/xvdf.
The operation has completed successfully.
Now,  your changes has been configured succesfuly, exit out of the gdisk console and do the same for the remaining disks.
```


7. Use lsblk utility to view the newly configured partition on each of the 3 disks.

![5026](https://github.com/SnehaS28/Dev-project-1/blob/images/partition.png)


8. Install lvm2 package using sudo yum install lvm2. Run sudo lvmdiskscan command to check for available partitions.

   Note: In RedHat/CentOS a linux package manager is used, so we use yum command.
   ![5027](https://github.com/SnehaS28/Dev-project-1/blob/images/package-manager.png)

10. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1

```

10. Verify that your Physical volume has been created successfully by running sudo pvs

![5028](https://github.com/SnehaS28/Dev-project-1/blob/images/physicalvols.png)


11. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

```
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1

```

12. Verify that your VG has been created successfully by running 

```
sudo vgs

```

![5029](https://github.com/SnehaS28/Dev-project-1/blob/images/vg-webdata.png)


13. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of
 the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
 
```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg

```

14. Verify that your Logical Volume has been created successfully by running 

```
sudo lvs

```

![5030](https://github.com/SnehaS28/Dev-project-1/blob/images/vg-webdata.png)


15. Verify the entire setup

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk

```

16. Use mkfs.ext4 to format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

17. Create /var/www/html directory to store website files

```
sudo mkdir -p /var/www/html

```

18. Create /home/recovery/logs to store backup of log data

```
sudo mkdir -p /home/recovery/logs

```

19. Mount /var/www/html on apps-lv logical volume

```
sudo mount /dev/webdata-vg/apps-lv /var/www/html/

```

20. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before 
mounting the file system)

```
sudo rsync -av /var/log/. /home/recovery/logs/

```

21. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above 
is very important)

```
sudo mount /dev/webdata-vg/logs-lv /var/log

```

22. Restore log files back into /var/log directory

```
sudo rsync -av /home/recovery/logs/. /var/log

```

23. Update /etc/fstab file so that the mount configuration will persist after restart of the server.
```
sudo vi /etc/fstab

```

