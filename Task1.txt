Part One:

# pvcreate /dev/sdb /dev/sdc
# vgcreate -s 16M vg1 /dev/sdb /dev/sdc
# lvcreate -l 50 -n lvm01
# mkfs.ext4 /dev/vg1/lvm01
# blkid /dev/vg1/lv1
# vim /etc/fstab
# mkdir -p /mnt/data
UUID=xxxxxxxx /mnt/data ext4 defaults 0 0
# vim /etc/fstab
# mount -a
# mount

========================================================================================
Part Two:

[root@localhost ~]# useradd -u 601 user1
[root@localhost ~]# passwd user1
Changing password for user user1.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost ~]# groupadd TrainingGroup
[root@localhost ~]# usermod user1 -g TrainingGroup
[root@localhost ~]# id user1
uid=601(user1) gid=1003(TrainingGroup) groups=1003(TrainingGroup)
[root@localhost ~]#

==>  The user's login shell should be non interactive. (no ssh access to server) :
[root@localhost home]# vi /etc/ssh/sshd_config
then add this line at the end of the file:
DenyUsers user1
then save file

==> create user 2:
[root@localhost home]# useradd -G adm user2
[root@localhost home]# passwd user2
Changing password for user user2.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost home]# id user2
uid=1002(user2) gid=1004(user2) groups=1004(user2),4(adm)
[root@localhost home]#

==>create user 3:
[root@localhost home]# useradd -G adm user3
[root@localhost home]# passwd user3
Changing password for user user3.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost home]# id user3
uid=1003(user3) gid=1005(user3) groups=1005(user3),4(adm)
[root@localhost home]#

==> to make user3 with root permissions:
1)visudo
2)add <<user3 ALL = (ALL) ALL>> at the end of the file then save.

===============================================================================
part Three:

[root@server ~]# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:iFQVCYxtWgJ8tGPzfhEx64hYy00UcIQsnL266YceD4A root@server
The key's randomart image is:
+---[RSA 2048]----+
| o.*oBB+=o       |
|  = B== .+       |
|   oB*. o        |
|.  *oX + .       |
|E ..= = S        |
| ..  .   .       |
|  o+  . .        |
|  ++.  .         |
| oo..            |
+----[SHA256]-----+
[root@server ~]#ssh-copy-id -i /root/.ssh/id_rsa.pub user3@172.20.50.136
[root@server ~]#ssh user3@172.20.50.136
[user3@client]#
===================================================================================

Part Four:
cp /etc/fstab /var/tmp/admin
==>allow user1 read and write : setfacl -m u:user1:rwx /var/tmp/admin
==>to deny user2 from doing nothing : setfacl -m u:user2:--- /var/tmp/admin

===================================================================================
Part Five:

#setenforce 1
https://vceguide.com/create-a-volume-group-and-set-16m-as-a-extends-and-divided-a-volume-group-containing-50-extends-on-volume-group-lv-make-it-as-ext4-file-system-and-mounted-automatically-under-mntdata/

====================================================================================
Part Six:

1)#vim myscript.sh
2)inside myscript.sh ⇒
#!/bin/bash
runtime="10 minute"
endtime=$(date -ud "$runtime" +%s)
while [[ $(date -u +%s) -le $endtime ]]
do
    echo -n "Time Now: `date +%H:%M:%S`" >> /tmp/scriptoutputfile
    echo -n "Sleeping for 1 second" >> /tmp/scriptoutputfile
    sleep 1s
done
3)#chmod +x myscript.sh
4)#./myscript.sh &
5)ps
6)copy process(myscript.sh) id ⇒ 20923
7)kill 20923

===============================================
Part Seven:

#yum install tmux
#yum install httpd
#yum install
#sudo wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
#sudo rpm -Uvh mysql80-community-release-el7-3.noarch.rpm
#sudo yum install mysql-server
#sudo systemctl start mysqld
#sudo systemctl status mysqld
#yum install createrepo
#mkdir /repo1
#wget https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/ -P /repo1
#createrepo /repo1
#vim /etc/yum.repos.d/exalt.repo
[Exalt]
name=Exalt Repository
baseurl=http:file:///repo1/
enabled=0
gpgcheck=1

#yum-config-manager --disablerepo=*
#yum-config-manager --enable exalt.repo
#yum install php php-pear php-cgi php-common php-mbstring php-snmp php-gd php-pecl-mysql php-xml php-mysql php-gettext php-bcmath
#vim /etc/php.ini
date.timezone = Asia/Palestine
#yum install zabbix
#yum install zabbix-web
#yum install zabbix-server
#yum install zabbix-agent


==========================================================
Part Eight: 
#firewall-cmd --zone=public --add-port=80/tcp --permanent
#firewall-cmd --reload
#firewall-cmd --zone=public --add-port=80/tcp --permanent
#firewall-cmd --reload
#firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='172.20.50.176' reject"
or 
#vi /etc/hosts.allow
ssh: 172.20.50.176
#vi /etc/hosts.deny
ssh: ALL

===============================
Part Nine:

#crontab -e
30 11 * * * /tmp/getusers.sh
In /tmp/getusers.sh
#!/bin/bash
date=$(date)
users=$(users)
echo “ $date - $users “ >> /tmp/loggedin_users
#chmod +x getusers.sh

=================================

Part Ten:

#yum install mariadb-server
#systemctl start mariadb
#iptables-save > iptables.txt
#iptables -A INPUT -p tcp --dport 3306 -j ACCEPT
# mysql -u root
>CREATE USER 'user1'@localhost IDENTIFIED BY 'password1';
>GRANT ALL PRIVILEGES ON *.* TO 'user1'@localhost IDENTIFIED BY 'password1';
>FLUSH PRIVILEGES;
>CREATE DATABASE studentsdb
>use studentsdb
>CREATE TABLE Students (studentNum Varchar(20), firstName Varchar(100), lastName Varchar(100), enrolledProgramme Varchar(100),expectedGraduationYear Varchar(20),PRIMARY KEY(studentNum));
>INSERT INTO Students VALUES 
->('110-001', 'Allen', 'Brown', 'mechanical', 2017),
->('110-002', 'David', 'Brown', 'mechanical', 2017),
->('110-003', 'Mary', 'Green', 'mechanical', 2018),
->('110-004', 'Dennis', 'Green', ‘electrical’, 2018),
->('110-005', 'Joseph', 'Black', ‘electrical’, 2018),
->('110-006', 'Dennis', 'Black', ‘electrical’, 2020),
->('110-007', 'Ritchie', 'Salt', ‘computer science’, 2020)
->('110-008', 'Robert', 'Salt', ‘computer science’, 2020),
->('110-009', 'David', 'Suzuki', ‘computer science’, 2020),
->('110-010', 'Mary', 'Chen', ‘computer science’, 2020);














