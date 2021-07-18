## Xiaomi Mix2s Install Windows11 Arm

###  一、重启手机（按电源+音量加键）进入TWRP并连接电脑

### 二、用ADB命令对手机磁盘进行分区

这可能会损坏你的设备，不慎变砖请使用9008恢复你的设备



```shell
cp /sdcard/parted /sbin/ && chmod 755 /sbin/parted
umount /data && umount /sdcard
parted /dev/block/sda
resizepart 17 50GB #17是userdata分区号 
mkpart esp fat32 50GB 50.5GB
mkpart pe fat32 50.5GB 56GB
mkpart win ntfs 56GB 125GB
```

```shell
1611MB 59.1GB

mkpart esp fat32 1611MB 2111MB         //500MB
mkpart pe fat32 2111MB 3135MB           //1024MB
mkpart win ntfs 3135MB 52287MB          //48GB
mkpart userdata ext4 52287MB 59.1GB  //


mkpart esp fat32 1611MB 1911MB         //300MB
mkpart pe fat32 1911MB 2935MB           //1024MB
mkpart win ntfs 2935MB 54135MB          //50GB
mkpart userdata ext4 54135MB 59.1GB  //


mkpart esp fat32 1611MB 1867MB         //256MB
mkpart pe fat32 1867MB 2891MB          //2048MB
mkpart win ntfs 2891MB 51.1MB            //48GB
mkpart userdata ext4 51.1GB 59.1GB  //8G

mkpart esp fat32 1611MB 1739MB         //128MB
mkpart pe fat32 1739MB 2763MB           //1024MB
mkpart win ntfs 2763MB 35531MB          //32GB
mkpart userdata ext4 35531MB 59.1GB  //

set 21 esp on
```

### 步骤三：重启TWRP，格式化新分区

某些TWRP分区路径是/dev/block/bootdevice/by-name/pe

```shell
mkfs.fat -F32 -s1 /dev/block/by-name/pe
mkfs.fat -F32 -s1 /dev/block/by-name/esp
mkfs.ntfs -f /dev/block/by-name/win
mke2fs -t ext4 /dev/block/by-name/userdata
```

### 步骤四：再次重启TWRP，挂载PE分区到 /mnt

```shell
mount /dev/block/by-name/pe /mnt
cp -r /sdcard/* /mnt
```

### 步骤五：进入fastboot，启动uefi ，这里提供三种方法，推荐第一种

```shell
临时启动uefi
fastboot boot boot-polaris.img
刷入到当前boot分区
fastboot flash boot boot-xxx.img
刷入到recovery分区
fastboot flash recovery boot-xxx.img
```

