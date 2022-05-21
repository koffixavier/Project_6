# Project_6
Web Solution With WordPress
## Step 1:Launched an 2 EC2 instances that will serve as "Web Server" and "Database Server". Create 3 volumes in the same availability zone  as the Web Server EC2, and 3 volumes for the Database Server, each of 10 GiB

![Screen Shot 2022-05-18 at 12 58 28 AM](https://user-images.githubusercontent.com/101080172/169388884-cd327988-44a1-46cb-bdc4-e719abfdbf5b.png)
![Screen Shot 2022-05-18 at 12 58 52 AM](https://user-images.githubusercontent.com/101080172/169388922-023d5731-85e3-477b-b698-c8bae8e48b67.png)

- Used `lsblk` command to inspect what block devices are attached to the server

![Screen Shot 2022-05-21 at 2 48 54 AM](https://user-images.githubusercontent.com/101080172/169639675-bbbf2259-2440-4d44-b745-b2d7644edd6e.png)

- Used `df-h` to see all mounts and tree space on the server

![Screen Shot 2022-05-21 at 2 50 29 AM](https://user-images.githubusercontent.com/101080172/169639763-aa749e78-4c8a-4051-8edc-66d6db7859d1.png)

- Created a single partion on each of the 3 disks

![Screen Shot 2022-05-21 at 3 03 00 AM](https://user-images.githubusercontent.com/101080172/169640111-fcca8006-6362-47b5-bc2d-a9d810fc0941.png)

![Screen Shot 2022-05-21 at 3 06 05 AM](https://user-images.githubusercontent.com/101080172/169640200-e08da43e-468e-4bf5-a2cb-cc8b14c535d1.png)

- Used lsblk to view the configurated partion on each disk

![Screen Shot 2022-05-21 at 3 08 11 AM](https://user-images.githubusercontent.com/101080172/169640273-5eed7e68-858f-419e-968c-b34fc4e43a20.png)

- Installed lvm2 package using sudo `yum install lvm2`. Runned `sudo lvmdiskscan` command to check for available partitions.

![Screen Shot 2022-05-21 at 3 10 10 AM](https://user-images.githubusercontent.com/101080172/169640366-917d1335-7549-4c33-8b32-7b6cf00bd6db.png)

![Screen Shot 2022-05-21 at 3 11 01 AM](https://user-images.githubusercontent.com/101080172/169640381-09bbcacc-4f94-4caf-9c07-78aa52dc4ba5.png)


- Used `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM and verified if created by using `sudo pvs`


![Screen Shot 2022-05-21 at 3 14 19 AM](https://user-images.githubusercontent.com/101080172/169640481-5be87c13-e9b9-44f3-a66b-a6b3f458a5b9.png)


-Used `vgcreate` to add all 3 PVs to a volume group (VG). Named the VG webdata-vg; `sudo vgs` to verified if the VG have be created

![Screen Shot 2022-05-21 at 3 19 12 AM](https://user-images.githubusercontent.com/101080172/169640772-92ac9871-7852-4eaa-a406-28d8ff35503f.png)

-Used `lvcreate` utility to create 2 logical volumes and used `sudo lvs` to verify their creation

![Screen Shot 2022-05-21 at 3 22 04 AM](https://user-images.githubusercontent.com/101080172/169640821-4253d3fe-6abc-4516-8604-98730f6e74b1.png)


- Verified the entire setup using `sudo vgdisplay -v #view complete setup - VG, PV, and LV`

![Screen Shot 2022-05-21 at 3 31 44 AM](https://user-images.githubusercontent.com/101080172/169641099-d5302ade-b6dd-422b-81b2-174515fb4796.png)
![Screen Shot 2022-05-21 at 3 31 57 AM](https://user-images.githubusercontent.com/101080172/169641102-d1ee1ba4-36a7-46f1-8d57-7f10c3092ddc.png)

![Screen Shot 2022-05-21 at 3 32 20 AM](https://user-images.githubusercontent.com/101080172/169641106-b4a55695-6e97-4ee4-828a-7498d8e25762.png)

- Used mkfs.ext4 to format the logical volumes with ext4 filesystem


![Screen Shot 2022-05-21 at 3 34 23 AM](https://user-images.githubusercontent.com/101080172/169641270-0596529f-1c8b-4b90-9c6a-030b7b3121d4.png)


- Created /var/www/html directory to store website files `sudo mkdir -p /`
 - Created /home/recovery/logs to store backup of log data `sudo mkdir -p /home/recovery/logs`
 - Mounted /var/www/html on apps-lv logical volume `sudo mount /dev/webdata-vg/apps-lv /var/www/html/`
 - Used rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs `sudo rsync -av /var/log/. /home/recovery/logs/`

![Screen Shot 2022-05-21 at 3 51 54 AM](https://user-images.githubusercontent.com/101080172/169641824-14cc904c-faea-4d23-8758-5d43ed0fc5b7.png)


- Mount /var/log on logs-lv logical volume `sudo mount /dev/webdata-vg/logs-lv /var/log` and Restored log files back into /var/log directory

![Screen Shot 2022-05-21 at 4 00 00 AM](https://user-images.githubusercontent.com/101080172/169642180-6c2a7a52-f074-4313-ba00-751b1c60953a.png)


- Updated /etc/fstab file so that the mount configuration will persist after restart of the server
  - first grabbed the UUID of the device, edited and added to our /etc/fstab
 
![Screen Shot 2022-05-21 at 4 17 58 AM](https://user-images.githubusercontent.com/101080172/169642849-852dda2f-0709-4434-a16a-074aed2d21d4.png)

- Tested the configuration and reloaded the daemon

 ````
 sudo mount -a
 sudo systemctl daemon-reload 
 ````
- Verified the setup by running `df -h`

![Screen Shot 2022-05-21 at 4 26 18 AM](https://user-images.githubusercontent.com/101080172/169643118-1924ba74-6e4e-414a-a4cf-ac60d31dbd62.png)

## Step 2 : Prepared the Database Server

- Repeatead the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/

![Screen Shot 2022-05-21 at 4 40 32 AM](https://user-images.githubusercontent.com/101080172/169643570-218dad3f-a84e-4d1b-bf4b-53603225973f.png)

![Screen Shot 2022-05-21 at 4 41 01 AM](https://user-images.githubusercontent.com/101080172/169643573-89768a90-2c57-4e89-ac75-730e5829fa7e.png)

![Screen Shot 2022-05-21 at 4 54 42 AM](https://user-images.githubusercontent.com/101080172/169644057-8344281d-b242-48e2-b00f-147cd5bfb17c.png)

![Screen Shot 2022-05-21 at 4 56 06 AM](https://user-images.githubusercontent.com/101080172/169644098-6d299749-e860-45a2-bea1-4ddc707443ab.png)

![Screen Shot 2022-05-21 at 5 01 45 AM](https://user-images.githubusercontent.com/101080172/169644283-f3c786f8-5aac-4cc0-89e7-9fe5d626459b.png)

![Screen Shot 2022-05-21 at 5 04 09 AM](https://user-images.githubusercontent.com/101080172/169644439-1a86640a-f849-4117-bacc-d5a902cef4ee.png)

![Screen Shot 2022-05-21 at 5 04 27 AM](https://user-images.githubusercontent.com/101080172/169644445-dc47bbe9-945c-4e01-afd4-f6a7738913f8.png)

![Screen Shot 2022-05-21 at 5 05 02 AM](https://user-images.githubusercontent.com/101080172/169644452-7a406ff2-c851-424c-a577-488ee5fe042f.png)

![Screen Shot 2022-05-21 at 5 17 09 AM](https://user-images.githubusercontent.com/101080172/169645076-613b115d-99ed-4836-b39a-a071af34de30.png)

![Screen Shot 2022-05-21 at 5 24 30 AM](https://user-images.githubusercontent.com/101080172/169645126-e153e679-ba0c-4771-9412-69f06948fa97.png)



## Step 3 : Installed WordPress on your Web Server EC2
-Update the repository `sudo yum -y update`

![Screen Shot 2022-05-21 at 5 31 53 AM](https://user-images.githubusercontent.com/101080172/169645429-a334d599-920e-4297-8ab7-c3866199cf31.png)


- Install wget, Apache and it’s dependencies `sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

![Screen Shot 2022-05-21 at 5 39 47 AM](https://user-images.githubusercontent.com/101080172/169645692-09bf99a5-3544-470f-9279-9cc3fa6cb6d5.png)


- Started Apache

![Screen Shot 2022-05-21 at 12 35 11 PM](https://user-images.githubusercontent.com/101080172/169661148-3bb72961-f6f0-4bc0-a725-1d2406bff6c6.png)


- installed  PHP and it’s depemdencies and Restarted Apache

![Screen Shot 2022-05-21 at 12 36 45 PM](https://user-images.githubusercontent.com/101080172/169661335-c8c486a1-a65e-4c06-90a7-3dcb0993fbeb.png)

![Screen Shot 2022-05-21 at 12 39 00 PM](https://user-images.githubusercontent.com/101080172/169661341-3cbe7228-e79b-496c-a9e4-e9e24f079b76.png)

![Screen Shot 2022-05-21 at 12 49 02 PM](https://user-images.githubusercontent.com/101080172/169661512-137a247b-2f85-45fb-8d3d-1938dee161c0.png)

![Screen Shot 2022-05-21 at 12 51 42 PM](https://user-images.githubusercontent.com/101080172/169661521-b2119c86-fb64-47c2-b965-a7b5161ccdbf.png)

- Download wordpress and copy wordpress to var/www/html
````
mkdir wordpress
  cd   wordpress
  sudo wget http://wordpress.org/latest.tar.gz
  sudo tar xzvf latest.tar.gz
  sudo rm -rf latest.tar.gz
  cp wordpress/wp-config-sample.php wordpress/wp-config.php
  cp -R wordpress /var/www/html/
  
  ````
  ![Screen Shot 2022-05-21 at 12 57 10 PM](https://user-images.githubusercontent.com/101080172/169662587-e863e8b5-2723-41e9-9e51-a0ebbf185dd0.png)
  
![Screen Shot 2022-05-21 at 1 13 41 PM](https://user-images.githubusercontent.com/101080172/169662599-13233159-8ff1-4d16-beed-1cd2be7d440a.png)

![Screen Shot 2022-05-21 at 1 14 48 PM](https://user-images.githubusercontent.com/101080172/169662604-af57c78d-b1bc-4b85-b904-3ef365b9917e.png)

- Configured SELinux Policies

````
  sudo chown -R apache:apache /var/www/html/wordpress
  sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
  sudo setsebool -P httpd_can_network_connect=1
 
 ````
 ![Screen Shot 2022-05-21 at 1 27 23 PM](https://user-images.githubusercontent.com/101080172/169662708-4f29fcc5-9a79-48b8-81cb-3b070f6b3db1.png)
 
 ## Step4: Install MySQL on the DB Server Instance 
 
 ![Screen Shot 2022-05-21 at 1 32 18 PM](https://user-images.githubusercontent.com/101080172/169663721-64dc442a-cc57-4804-a866-3175454993e5.png)

 ![Screen Shot 2022-05-21 at 1 35 59 PM](https://user-images.githubusercontent.com/101080172/169663713-b2e8415a-af49-42da-9bf3-f2d4834ff30f.png)

## Step5: Configure DB to work with WordPress

````
sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
````
 ![Screen Shot 2022-05-21 at 1 54 58 PM](https://user-images.githubusercontent.com/101080172/169663632-2214cffb-e537-48a2-858d-7ae8819f391d.png)
 
## Step 6 — Configure WordPress to connect to remote database.
 
 ![Screen Shot 2022-05-21 at 2 31 24 PM](https://user-images.githubusercontent.com/101080172/169665727-a6ee0900-1bb9-43f3-94d3-6c8f924a142b.png)

![Screen Shot 2022-05-21 at 2 57 09 PM](https://user-images.githubusercontent.com/101080172/169665761-7ec29d83-3152-48a3-90f1-6dd1b897c49c.png)

![Screen Shot 2022-05-21 at 2 56 24 PM](https://user-images.githubusercontent.com/101080172/169665786-a7b5f808-0426-4ca7-8641-ffbeb2496561.png)

![Screen Shot 2022-05-21 at 2 55 31 PM](https://user-images.githubusercontent.com/101080172/169665916-bb63ef67-060e-4730-9719-092e53eef2ca.png)



 
