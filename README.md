# chia-obsfs


# step1. use the ghostos of obsfs&centos to create ECS in huaweicloud

#download the ghostos
https://hicloudshanghai.obs.cn-east-3.myhuaweicloud.com/chia-ios-centos.qcow2

#make slef ghostos fllow next link:
https://support.huaweicloud.com/usermanual-ims/zh-cn_topic_0030713191.html

#use slef ghostos create ECS
![image](https://user-images.githubusercontent.com/32640053/116501938-bd7c9380-a8e4-11eb-8e7f-756a500c7ce0.png)


# Step2.  mount a disk, fllow next link:
https://support.huaweicloud.com/qs-evs/evs_01_0033.html

#for example

fdisk /dev/vdb

partprobe

mkfs -t ext4 /dev/vdb1

mount /dev/vdb1 /mnt/ssd0

# Step3. configure obsfs

#config AK SK to /etc/passwd-obsfs,like this:

AG*************:m***************************

#mkdir a dir for temp use during chia plot
mkdir /mnt/ssd0/temp/

#mkdir a dir for obsfs mount
mkdir /mnt/obsfs

#configure /etc/obsfsconfig,the content like this:
cat /etc/obsfsconfig 
dbglogmode=1
dbglevel=warn
fuse_intf_log_level=3
cache_attr_switch_open=1
cache_attr_valid_ms=600000
mining_local_fs_cache_path=/mnt/ssd0/temp/

# Step4. run obsfs
#command format:
./obsfs {bucketname} {mountdir} -o url={obs region domain} -o passwd_file=/etc/passwd-obsfs -o big_writes -o max_write=104857600 -o max_background=100 -o use_ino

#example:
./obsfs chia-obs /mnt/obsfs -o url=http://obs.cn-east-3.myhuaweicloud.com -o passwd_file=/etc/passwd-obsfs -o big_writes -o max_write=104857600 -o max_background=100 -o use_ino

#creat dir for chia plot

mkdir /mnt/obsfs/results/ 
mkdir /mnt/obsfs/plot/

# Step5. config chia 

cd /home/chia-blockchain/
. ./active
chia init
chia keys generate

#do chia plot use obsfs dir, the "-t /mnt/obsfs/plot/" MUST NOT BE CHANGE!!!

nohup chia plots create -k 32 -n 1 -t /mnt/obsfs/plot/ -d /mnt/obsfs/results/  >/home/run.log 2>&1 &


