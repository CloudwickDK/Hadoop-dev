
How to view File Blocks

1. Find out and run the command to view the location and block details of a file on HDFS

hadoop fsck /folder -files -blocks -locations -racks

2. Find the location of Namenode local dir and look at the file layout structure inside, calculate the frequency of editlog roll{new edit log being create} Submit the screenshot of the command and its output.

the location of the NameNode namefs.namedefault.name/name
NameNode metadata storage namedfs.name.dir/name

In file hdfs-site.xml
cd /hadoop/hdfs/namenode/current

ls -lrt
stat c%Y

Problem encountered: I have to change property: hdfs.namenode.acls.enabled to true.

vi /etc/hadoop/2.4.0.0-169/0/hdfs-site.xml

and add the property and then restart namenode service from ambari 

Also tried hdfs hdfs dfsadmin -refreshNodes

Also tried restarting 
hdfs dfsadmin -safemode enter
hdfs dfsadmin -saveNamespace

but when i run

hdfs dfs -setfacl -m user:userA:rwx /assignment1/asign1.txt

it still returns: Support for ACLs has been disabled by setting dfs.namenode.acls.enabled to false



hdfs dfs -mkdir /assignment1
hdfs dfs -chown userA /assignment1/asign1.txt

[root@master1 ~]# cat /etc/gr
group      group-     grub.conf
[root@master1 ~]# cat /etc/gr
group      group-     grub.conf
[root@master1 ~]# cat /etc/group | grep desp
desp:x:1003:
[root@master1 ~]# usermod -G desp userA      (adds userA to group desp)
[root@master1 ~]# cat /etc/group | grep desp
desp:x:1003:userA

[root@master1 desp]# sudo -u hdfs hdfs dfs -ls /assignment1
Found 1 items
-rw-r--r--   3 userA hdfs         51 2016-05-05 10:16 /assignment1/asign1.txt
[root@master1 desp]# sudo -u hdfs hdfs dfs -chgrp -R desp /assignment1
[root@master1 desp]# sudo -u hdfs hdfs dfs -ls /assignment1
Found 1 items
-rw-r--r--   3 userA desp         51 2016-05-05 10:16 /assignment1/asign1.txt
[root@master1 desp]# sudo -u hdfs hdfs dfs -chown -R userA /assignment1    (make userA the owner of folder assignment1, the ownner before was hdfs)
[root@master1 desp]# sudo -u hdfs hdfs dfs -chmod -R 700 /assignment1

[root@master1 desp]# sudo -u userA hdfs dfs -ls /assignment1
Found 1 items
-rwx------   3 userA desp         51 2016-05-05 10:16 /assignment1/asign1.txt
[root@master1 desp]# useradd userB
[root@master1 desp]# sudo -u userB hdfs dfs -ls /assignment1
ls: Permission denied: user=userB, access=READ_EXECUTE, inode="/assignment1":userA:desp:drwx------
[root@master1 desp]# usermod -G desp userB
[root@master1 desp]# sudo -u userA hdfs dfs -chmod -R 770 /assignment1

[root@master1 ~]# groupadd groupA
[root@master1 ~]# usermod -G groupA userA
[root@master1 ~]# cat /etc/group | grep userA
userA:x:1007:
groupA:x:1009:userA
[root@master1 ~]# cat /etc/group | grep desp
desp:x:1003:userB
[root@master1 ~]# usermod -a -G desp userA   (-a adds the user to this group and does not remove him from any other groups)
[root@master1 ~]# cat /etc/group | grep desp
desp:x:1003:userB,userA
[root@master1 ~]# cat /etc/group | grep groupA
groupA:x:1009:userA

















Commands' definition:

getfacl

Usage: hadoop fs -getfacl [-R] <path>

Displays the Access Control Lists (ACLs) of files and directories. If a directory has a default ACL, then getfacl also displays the default ACL.

Options:

-R: List the ACLs of all files and directories recursively.
path: File or directory to list.
Examples:

hadoop fs -getfacl /file
hadoop fs -getfacl -R /dir
Exit Code:

Returns 0 on success and non-zero on error.


getfattr

Usage: hadoop fs -getfattr [-R] -n name | -d [-e en] <path>

Displays the extended attribute names and values (if any) for a file or directory.

Options:

-R: Recursively list the attributes for all files and directories.
-n name: Dump the named extended attribute value.
-d: Dump all extended attribute values associated with pathname.
-e encoding: Encode values after retrieving them. Valid encodings are “text”, “hex”, and “base64”. Values encoded as text strings are enclosed in double quotes ("), and values encoded as hexadecimal and base64 are prefixed with 0x and 0s, respectively.
path: The file or directory.
Examples:

hadoop fs -getfattr -d /file
hadoop fs -getfattr -R -n user.myAttr /dir
Exit Code:

Returns 0 on success and non-zero on error.