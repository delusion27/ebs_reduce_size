# ebs_reduce_size

Because EBS volumes currently only support expansion and cannot be directly reduced, this script can automatically reduce the capacity of EBS. The principle of implementation is to first create a new EBS volume and mount it on EC2, synchronize the data to the new data disk, and then unmount the old EBS volume. The execution mode and results of this script are as follows:

----------------------------------------------------------------------------
```shell
$ python3 ebs_reduce_size.py --instance_id i-02896be77fc1c18ee --instance_ip 52.80.240.170 --origin_volume_id vol-0eef81a7683330018 --az cn-north-1a --newvolumesize 6 --source_data_mountpoint_path /data1
```

```shell
Please make sure you have backup the instance to AMI, if you have done, enter yes.(yes/no): yes
Start EBS Resizing!
vol-04ce7c93bb59764f0 is creating...
vol-04ce7c93bb59764f0 was created successfully
meta-data=/dev/sdk               isize=512    agcount=4, agsize=393216 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=0
data     =                       bsize=4096   blocks=1572864, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
Sync the data from original volume to new volume.
sending incremental file list
./
file1
              0 100%    0.00kB/s    0:00:00 (xfr#1, to-chk=2/4)
file2
              0 100%    0.00kB/s    0:00:00 (xfr#2, to-chk=1/4)
lost+found/
Remount back to the source file.
Remove the tmp file.
vol-0eef81a7683330018 detached successfully
i-02896be77fc1c18ee restarted successfully
```


Note: The execution of this script requires several parameters 
         -instance_id: the id of the instance to be operated 
         -origin_volume_id: the id of the volume to be replaced 
         -az: the availability zone where the new ebs volume is created. It is consistent with the availability zone where the EC2 instance is located 
         -newvolumesize: the size of the newly created EBS volume (you need to select the appropriate new volume size) 
         -source_data_mountpoint_path: the mount point path of the original data disk, That is, the path of the original data to be synchronized to the new disk in the operating system

Because it involves To the data disk, please be sure to complete the test in the current test environment before applying it to other machines. When the program starts, you will be prompted to do an AMI backup of the instance first. 
         1. The script is currently only applicable to the Linux operating system
         2. During the execution of the script, the corresponding EC2 will have a stop and restart process, so there will be down time
         3. This The script currently only applies to data disks, and the reduction of the root disk does not apply. 
         4. The script does not assume roles. Please ensure that you have aws cli configured with proper credentials 
         5. The default device name of the newly created EBS volume is

