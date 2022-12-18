PROJECT 6

WEB SOLUTION WITH WORDPRESS.
- For this project, i will be using the RedHat OS to launch my instance and I will be connecting via 'ec2-user' user. I created 2 EC2 Instances in AWS and named them;
- Web-Server
- Database-Server

I created 3 volumes of 10gb on my Web-Server and also attached all 3 volumes, one by one to my web server EC2 instance.
<img width="1440" alt="Screenshot 2022-11-29 at 11 09 49 AM" src="https://user-images.githubusercontent.com/115854902/208303061-bd3844fc-549b-48ca-9cb8-c7d600d4faf7.png">
Next, I went ahead to open my linux terminal to begin my configuration.
- I also used the 'lsblk' command to inspect that block devices have been attached to the server.
- I also created a single partition on each of the 3disks using the gdisk utility.
- I used the 'lsblk' utility to view and confirm my newly configured partition on each of the 3disks.

<img width="718" alt="Screenshot 2022-11-29 at 11 22 11 AM" src="https://user-images.githubusercontent.com/115854902/208305856-c842404a-75dc-4531-bb2d-72e4969c3a61.png">

<img width="718" alt="Screenshot 2022-11-29 at 11 51 12 AM" src="https://user-images.githubusercontent.com/115854902/208305910-59aeb6d7-178f-4319-8b69-a29593edd25b.png">
<img width="718" alt="Screenshot 2022-11-29 at 11 51 55 AM" src="https://user-images.githubusercontent.com/115854902/208306011-544bc8bc-9bbd-4043-8483-bedc1819eee7.png">
<img width="718" alt="Screenshot 2022-11-29 at 11 52 44 AM" src="https://user-images.githubusercontent.com/115854902/208306022-df77c42a-08e8-4688-b49b-7c79442154e9.png">
<img width="718" alt="Screenshot 2022-11-29 at 11 53 28 AM" src="https://user-images.githubusercontent.com/115854902/208306042-0800dafe-9b62-4e54-91ed-3d1e3532336e.png">

The next step was to install the lvm2 package using "sudo yum install lvm2". I also checked for available partitions using the command "sudo lvmdiskscan".

<img width="718" alt="Screenshot 2022-11-29 at 11 58 46 AM" src="https://user-images.githubusercontent.com/115854902/208306400-95c08534-5019-41af-b7b1-7b862bb15b91.png">
<img width="718" alt="Screenshot 2022-11-29 at 12 00 44 PM" src="https://user-images.githubusercontent.com/115854902/208306444-22ce757b-ef34-469f-ae52-ca83f57dc033.png">

I went ahead to use the pvcreate utility to mark each of the 3disks as physical volumes to be used by the LVM and also verified that the physical volumes have been successfully created with the command "sudo pvs".

<img width="718" alt="Screenshot 2022-11-29 at 12 04 11 PM" src="https://user-images.githubusercontent.com/115854902/208306655-f301841c-217b-47f1-a022-c607095a81db.png">
<img width="718" alt="Screenshot 2022-11-29 at 12 05 35 PM" src="https://user-images.githubusercontent.com/115854902/208306763-f3ff7630-9f7d-4928-9403-aa201d877542.png">

I added all 3 PVs to a volume group using the vgcreate utility and named the VG "webdata-vg". I also verified using the sudo vgs command.

<img width="718" alt="Screenshot 2022-11-29 at 12 07 47 PM" src="https://user-images.githubusercontent.com/115854902/208306956-744a212d-482d-446f-9ac1-82e283f98213.png">

Next, was to create 2 logical volumes named apps-lv and logs-lv and use half of the PV size for them. I also went ahead to verify that logical volumes have been successfully created.
- I also verified the entire set up using the lsblk command.
<img width="718" alt="Screenshot 2022-11-29 at 12 09 48 PM" src="https://user-images.githubusercontent.com/115854902/208307115-2f7412a3-4c16-4c67-a55f-c2bc1f7a48ad.png">
<img width="718" alt="Screenshot 2022-11-29 at 12 10 42 PM" src="https://user-images.githubusercontent.com/115854902/208307152-70f0a726-21a7-412d-83ae-be311d35566f.png">

<img width="718" alt="Screenshot 2022-11-29 at 12 13 52 PM" src="https://user-images.githubusercontent.com/115854902/208307369-e6241c68-afb4-43dd-ad79-6143da8e8357.png">
<img width="718" alt="Screenshot 2022-11-29 at 12 14 52 PM" src="https://user-images.githubusercontent.com/115854902/208307416-e6202ebe-5478-437e-939f-194fae086bd4.png">

Next step was to use mkfs.ext4 to format the logical volumes with ext4 file system.

<img width="718" alt="Screenshot 2022-11-29 at 12 18 38 PM" src="https://user-images.githubusercontent.com/115854902/208307662-e45e93f9-2ae9-41c9-a384-747c2e36c6d3.png">

I also created /var/www/html directory to store websites file.
To store backup of log data, I created /home/recovery/logs
I mounted /var/www/html on apps-lv logical volume.

<img width="718" alt="Screenshot 2022-11-29 at 12 22 29 PM" src="https://user-images.githubusercontent.com/115854902/208307906-a2b5315c-713a-4822-acea-7170513afb43.png">

I used the rsync utility to back up all the files in the log directory /var/log into /home/recovery/logs.
- I went ahead to mount /var/log on logs-lv logical volume
- Next, was to restore log files back into /var/log directory 

<img width="718" alt="Screenshot 2022-11-29 at 12 25 47 PM" src="https://user-images.githubusercontent.com/115854902/208308498-8c9bd4eb-85ac-4a50-ba38-67ee702e1936.png">

- Then I updated my /etc/fstab file so that the mount configuration will persist after i restart the server. The UUID of the device will be used to update the /etc/fstab file. To get the UUID, we will sudo blkid, then copy the UUID.
- Next, we 'sudo vi /etc/fstab' then update it using the UUID.
- The next step is to test the configuration and reload the daemon. I verified my setup by running 'df-h'.

<img width="718" alt="Screenshot 2022-11-29 at 12 41 27 PM" src="https://user-images.githubusercontent.com/115854902/208308780-849ea7c3-24b3-436f-909a-e111778e7565.png">

<img width="718" alt="Screenshot 2022-11-29 at 12 51 41 PM" src="https://user-images.githubusercontent.com/115854902/208308787-8bbd2671-b609-4fc6-81d0-cb361706219a.png">
<img width="718" alt="Screenshot 2022-11-29 at 12 54 46 PM" src="https://user-images.githubusercontent.com/115854902/208308792-fd0aab0f-15bc-4edd-8791-b2b176328c75.png">

STEP 2 

PREPARE THE DATABASE SERVER.
- I launched a second RedHat EC2 instance and named it Database Server. I repeated all steps as for the Web server but instead of apps-lv, I created db-lv, I created db-lv and mounted it to /db directory instead of /var/www/html 
- Please, kindly find images below;
<img width="725" alt="Screenshot 2022-11-29 at 1 10 37 PM" src="https://user-images.githubusercontent.com/115854902/208309350-e3863295-cbbe-4a6a-9fb0-73d0e6c14edc.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 12 09 PM" src="https://user-images.githubusercontent.com/115854902/208309371-c3e49041-a7e6-4329-b545-ad63ba6cf505.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 13 12 PM" src="https://user-images.githubusercontent.com/115854902/208309381-4db811e2-aa5d-422d-8468-f39d5fec94ab.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 15 06 PM" src="https://user-images.githubusercontent.com/115854902/208309487-8cae604e-0112-4fc3-8a5c-895b1a6faf09.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 15 58 PM" src="https://user-images.githubusercontent.com/115854902/208309496-ad5e874c-2027-4343-afde-a230e5f35239.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 34 44 PM" src="https://user-images.githubusercontent.com/115854902/208309516-aadb6c22-74f3-4f6b-b638-d5c7f5a93acd.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 35 16 PM" src="https://user-images.githubusercontent.com/115854902/208309536-1ea250b2-8716-451b-bb9e-abd6c8f85d32.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 51 28 PM" src="https://user-images.githubusercontent.com/115854902/208309573-c6154f6f-c965-41b0-8b23-d34c4ecbe233.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 56 29 PM" src="https://user-images.githubusercontent.com/115854902/208309615-01ff2475-a802-4d8f-843f-89de1dd3869e.png">
<img width="725" alt="Screenshot 2022-11-29 at 1 58 21 PM" src="https://user-images.githubusercontent.com/115854902/208309627-7dfdf2b6-a6ce-4fd8-8d08-693e71c90666.png">

STEP 3

INSTALL WORDPRESS ON YOUR WEB SERVER EC2
- The first thing to do here, is to update the repository by running 'sudo yum -y update'.

<img width="720" alt="Screenshot 2022-11-29 at 2 07 36 PM" src="https://user-images.githubusercontent.com/115854902/208310062-0be2560c-21e1-47ef-a6b9-e5cb0f79b4b2.png">
- Next was to install wget, Apache and it's dependencies.

<img width="720" alt="Screenshot 2022-11-29 at 2 08 44 PM" src="https://user-images.githubusercontent.com/115854902/208310156-4710f217-a060-4720-90c7-e7b6e2be274b.png">

Next step was to start Apache, we enable then start.

<img width="720" alt="Screenshot 2022-11-29 at 2 13 30 PM" src="https://user-images.githubusercontent.com/115854902/208310217-17c1045d-d3e0-4311-95cd-e19e0c7ca44e.png">

Followed by installing PHP and it's dependencies using the following commands below;
<img width="720" alt="Screenshot 2022-11-29 at 2 14 59 PM" src="https://user-images.githubusercontent.com/115854902/208310377-e33f496f-e621-4af5-9ef1-346083fd04f9.png">
<img width="720" alt="Screenshot 2022-11-29 at 2 16 39 PM" src="https://user-images.githubusercontent.com/115854902/208310392-da0f78e3-d777-403b-9a74-02fb035c7f38.png">
<img width="720" alt="Screenshot 2022-11-29 at 2 18 14 PM" src="https://user-images.githubusercontent.com/115854902/208310398-953d1bbc-97a2-480e-aa14-b7d77a5f524c.png">
<img width="720" alt="Screenshot 2022-11-29 at 2 20 07 PM" src="https://user-images.githubusercontent.com/115854902/208310407-d05dc49c-53e4-46ed-a2d6-cf3d34f60f24.png">

I went ahead to restart Apache 
- Then downloaded wordpress and copied wordpress to /var/www/html
- I configured SELinux Policies.

<img width="720" alt="Screenshot 2022-11-29 at 2 25 54 PM" src="https://user-images.githubusercontent.com/115854902/208310442-79cfdcb0-7d0b-4ede-8256-480803cab1a2.png">
<img width="720" alt="Screenshot 2022-11-29 at 2 31 25 PM" src="https://user-images.githubusercontent.com/115854902/208310725-36dbb2f9-8a56-4447-89bd-b4f21a789e35.png">

STEP 4

INSTALL MySQL ON YOUR DB SERVER EC2
- After updating and installing MySQL on my DB Server, I verified that the service was up and running.

<img width="721" alt="Screenshot 2022-11-29 at 2 33 02 PM" src="https://user-images.githubusercontent.com/115854902/208311397-1b6e5a72-5fc9-4a71-9104-913b78c9dec7.png">

STEP 5

CONFIGURE DB TO WORK WITH WORDPRESS.
- To do this, i sudo MySQL;
<img width="721" alt="Screenshot 2022-11-29 at 2 40 37 PM" src="https://user-images.githubusercontent.com/115854902/208311517-e3d2c578-8b0e-4031-8aff-22004d835df4.png">

STEP 6

CONFIGURE WORDPRESS TO CONNECT TO REMOTE DATABASE.

- I opened MySQL port 3306 on my DB Server EC2 and for extra security, I allowed access only my from Web Server's private IP address in the inbopund rules.
<img width="1440" alt="Screenshot 2022-11-29 at 2 43 28 PM" src="https://user-images.githubusercontent.com/115854902/208311644-f89cfdf3-e73e-4c77-b2ec-f040834475d6.png">

- I installed MySQL client and tested that i can connect from my Web Server to my DB Server by using MySQL Client. I also verified that i can successfully execute "SHOW DATABASES" command and i saw a list of existing databases.
- I changed permissions and configuration, so Apache can use WordPress.
- Next step was to enable TCP Port 80 in Inbound Rules configuration for my Web Server and enable access from everywhere.
- I access from my browser, the link to my wordpress.
<img width="720" alt="Screenshot 2022-11-29 at 8 10 29 PM" src="https://user-images.githubusercontent.com/115854902/208311892-0699dc15-af5c-4966-89bd-ef9a13126a02.png">

<img width="1440" alt="Screenshot 2022-11-29 at 9 37 15 PM" src="https://user-images.githubusercontent.com/115854902/208311954-76393915-aad2-4a1c-b115-73907e281967.png">
<img width="1440" alt="Screenshot 2022-11-29 at 9 42 48 PM" src="https://user-images.githubusercontent.com/115854902/208311971-d4ce01e0-7706-408c-8eaa-353f3e608c39.png">

<img width="1440" alt="Screenshot 2022-11-29 at 9 43 15 PM" src="https://user-images.githubusercontent.com/115854902/208311991-ba199124-8d5d-4380-bc2f-292b62a6a8ee.png">

<img width="1440" alt="Screenshot 2022-11-29 at 9 43 51 PM" src="https://user-images.githubusercontent.com/115854902/208312002-52b318d5-27ca-4f57-ace2-bf72d762f713.png">

<img width="1440" alt="Screenshot 2022-11-29 at 9 44 46 PM" src="https://user-images.githubusercontent.com/115854902/208312008-0dab5304-42dc-4f74-b272-7474dd25859b.png">













