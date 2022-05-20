# Project_6
Web Solution With WordPress
## Step 1:Launched an 2 EC2 instances that will serve as "Web Server" and "Database Server". Create 3 volumes in the same availability zone  as the Web Server EC2, and 3 volumes for the Database Server, each of 10 GiB

![Screen Shot 2022-05-18 at 12 58 28 AM](https://user-images.githubusercontent.com/101080172/169388884-cd327988-44a1-46cb-bdc4-e719abfdbf5b.png)
![Screen Shot 2022-05-18 at 12 58 52 AM](https://user-images.githubusercontent.com/101080172/169388922-023d5731-85e3-477b-b698-c8bae8e48b67.png)
- Used `lsblk` command to inspect what block devices are attached to the server
- ![Screen Shot 2022-05-18 at 12 59 37 AM](https://user-images.githubusercontent.com/101080172/169391342-4573cdae-66eb-4bda-8659-b6f8aac0668a.png)
- Used `df-h` to see all mounts and tree space on the server
![Screen Shot 2022-05-18 at 1 00 03 AM](https://user-images.githubusercontent.com/101080172/169392098-e7214005-4e3a-4bd2-bb2d-0c48683160a4.png)

- Created a single partion on each of the 3 disks

![Screen Shot 2022-05-18 at 1 ![Screen Shot 2022-05-18 at 1 01 10 AM](https://user-images.githubusercontent.com/101080172/169395505-1bfdc6d0-fcf5-4c64-97da-b1e4b51277d8.png)

![Screen Shot 2022-05-18 at 1 01 10 AM](https://user-images.githubusercontent.com/101080172/169395562-39804ca8-4e3c-435a-8ea6-ee20ea60cf5b.png)

![Screen Shot 2022-05-18 at 1 02 30 AM](https://user-images.githubusercontent.com/101080172/169395779-47a0a20d-c02c-42d3-a168-f7521582eda0.png)

- Used lsblk to view the configurated partion on each disk

![Screen Shot 2022-05-18 at 1 03 20 AM](https://user-images.githubusercontent.com/101080172/169396016-d057bd39-01c1-476f-9335-5c73e2112ea6.png)![Screen Shot 2022-05-18 at 1 04 36 AM](https://user-images.githubusercontent.com/101080172/169437042-a06a2f0f-6aa8-4557-a049-433bcc0ff14a.png)![Screen Shot 2022-05-18 at 1 04 36 AM](https://user-images.githubusercontent.com/101080172/169437095-385fc45b-b330-4756-b420-ad717bab62e2.png)



- Installed lvm2 package using sudo `yum install lvm2`. Runned `sudo lvmdiskscan` command to check for available partitions.

![Screen Shot 2022-05-18 at 1 03 39 AM](https://user-images.githubusercontent.com/101080172/169397725-9b876882-3db8-4e94-900c-bc28416828d3.png)

![Screen Shot 2022-05-18 at 1 04 36 AM](https://user-images.githubusercontent.com/101080172/169397837-f4f43a10-b1f5-4aa2-a65e-fcfd896ad522.png)

- Used `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
![Screen Shot 2022-05-18 at 1 04 36 AM](https://user-images.githubusercontent.com/101080172/169437103-c30adc6d-26a0-4a94-a05c-cbfc66669203.png)

-Used `vgcreate` to add all 3 PVs to a volume group (VG). Named the VG webdata-vg
- `sudo vgs` to verified if the VG have be created
- Used `lvcreate` utility to create 2 logical volumes

![Screen Shot 2022-05-18 at 1 05 12 AM](https://user-images.githubusercontent.com/101080172/169437457-eb2984fd-62ae-43d6-992d-8fcd3c2dc2af.png)

- Verified the entire setup using `sudo vgdisplay -v #view complete setup - VG, PV, and LV`

![Screen Shot 2022-05-18 at 1 05 32 AM](https://user-images.githubusercontent.com/101080172/169442673-898fa180-fb64-4b13-b458-f57bece204c1.png)

- Used mkfs.ext4 to format the logical volumes with ext4 filesystem

![Screen Shot 2022-05-18 at 1 05 58 AM](https://user-images.githubusercontent.com/101080172/169443817-f92def07-8480-43a0-b43b-ea417d4c01aa.png)

- Created /var/www/html directory to store website files `sudo mkdir -p /var/www/html`

- Created /home/recovery/logs to store backup of log data `sudo mkdir -p /home/recovery/logs`

- Mounted /var/www/html on apps-lv logical volume `sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

- Used rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs `sudo rsync -av /var/log/. /home/recovery/logs/`

![Screen Shot 2022-05-18 at 1 06 33 AM](https://user-images.githubusercontent.com/101080172/169446267-71a53bb3-cf04-4233-ad18-a90ee15106c7.png)

- Mount /var/log on logs-lv logical volume `sudo mount /dev/webdata-vg/logs-lv /var/log`

![Screen Shot 2022-05-18 at 1 06 55 AM](https://user-images.githubusercontent.com/101080172/169446324-44dbfdcb-8810-448e-823d-efa18de62ee7.png)

- Updated /etc/fstab file so that the mount configuration will persist after restart of the server.

- Verified your setup by running `df -h`

![Screen Shot 2022-05-18 at 1 09 16 AM](https://user-images.githubusercontent.com/101080172/169448355-b3ecd082-8187-46e6-8b45-8453f85ebffa.png)

![Screen Shot 2022-05-18 at 1 09 34 AM](https://user-images.githubusercontent.com/101080172/169448424-90c92fee-dc11-4e33-9940-ca1c56cecabf.png)

## Step 2 : Prepared the Database Server

- Repeatead the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/

## Step 3 : Installed WordPress on your Web Server EC2
-Update the repository `sudo yum -y update`

- Install wget, Apache and it’s dependencies `sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

![Screen Shot 2022-05-18 at 1 09 50 AM](https://user-images.githubusercontent.com/101080172/169581663-452db1aa-f061-437d-aa19-f81a4ac139cb.png)

![Screen Shot 2022-05-18 at 1 11 13 AM](https://user-images.githubusercontent.com/101080172/169581973-53fda356-75a9-49db-bdb9-32a015374b69.png)

- Started Apache

![Screen Shot 2022-05-18 at 1 11 47 AM](https://user-images.githubusercontent.com/101080172/169582143-cac0e783-7256-450c-954b-00becbd07fcc.png)

- installed  PHP and it’s depemdencies and Restarted Apache

![Screen Shot 2022-05-18 at 1 12 43 AM](https://user-images.githubusercontent.com/101080172/169582334-4ffb12e0-02f3-4d18-a057-faed9170b91c.png)

![Screen Shot 2022-05-18 at 1 12 06 AM](https://user-images.githubusercontent.com/101080172/169582382-75dd0225-696a-4137-a8dd-ce7e69019389.png)

![Screen Shot 2022-05-18 at 1 13 07 AM](https://user-images.githubusercontent.com/101080172/169582444-c95ea895-f850-4855-bb23-4f58bbd3b5be.png)

![Screen Shot 2022-05-18 at 1 13 30 AM](https://user-images.githubusercontent.com/101080172/169582496-1afee6e5-2d16-4ce2-9b7a-67f74e25c71c.png)

![Screen Shot 2022-05-18 at 1 13 58 AM](https://user-images.githubusercontent.com/101080172/169582593-35376074-d760-4d8b-93ce-618717a7de38.png)

- Download wordpress and copy wordpress to var/www/html

![Screen Shot 2022-05-18 at 1 14 23 AM](https://user-images.githubusercontent.com/101080172/169589688-450a04b2-3de6-43fc-aac5-a7a56f3abf2c.png)

![Screen Shot 2022-05-18 at 1 15 36 AM](https://user-images.githubusercontent.com/101080172/169589763-d6bc0b6b-55c4-47ac-8f6c-953552061638.png)


