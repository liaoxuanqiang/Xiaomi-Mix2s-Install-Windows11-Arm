## Xiaomi Mix2s Install Windows11 Arm

###  一、重启手机（按电源+音量加键）进入TWRP并连接电脑

### 二、借助parted工具对手机磁盘分区

- 首先将[parted](https://github.com/liaoxuanqiang/Xiaomi-Mix2s-Install-Windows11-Arm/blob/main/parted)文件复制到手机内置存储


```shell
cp /sdcard/parted /sbin/ && chmod 755 /sbin/parted  #复制parted到 /sbin目录
umount /data && umount /sdcard  #取消挂载userdata分区
parted /dev/block/sda
rm 21  #删除21号分区（userdata），这会删除手机所有数据
#删除userdata分区后新建磁盘分区大小起始到末尾为1611MB-59.1GB
mkpart esp fat32 1611MB 1739MB         #新建esp分区大小设为128MB
mkpart pe fat32 1739MB 2763MB          #新建pe分区大小设为1024MB
mkpart win ntfs 2763MB 35531MB         #新建win分区大小设为32GB
mkpart userdata ext4 35531MB 59.1GB    #新建userdata分区
or
resizepart 21 8GB  #21是userdata分区号
mkpart esp fat32 .... #新建esp分区
mkpart pe fat32 ....  #新建pe分区
mkpart win ntfs ....  #新建win分区
p  #新建分区后输入p检查分区情况
set 21 esp on  #设置esp分区为激活状态
```

### 三、手机重启到TWRP，格式化新建分区

```shell
reboot recovery  #重启到Twrp
mkfs.fat -F32 -s1 /dev/block/by-name/pe  #格式化pe分区为fat32格式
mkfs.fat -F32 -s1 /dev/block/by-name/esp #格式化esp分区为fat32格式
mkfs.ntfs -f /dev/block/by-name/win  #格式化win分区为ntfs格式
mke2fs -t ext4 /dev/block/by-name/userdata #格式化userdata分区为e2fs格式
```

### 四、格式化新建分区后再次重启到TWRP，挂载PE分区到 /mnt

```shell
reboot recovery #重启到Twrp
mount /dev/block/by-name/pe /mnt #挂载pe分区到 /mnt目录
```

- 解压[20h2pe_new.zip](https://pan.baidu.com/s/1Pgaz-bdTiOKFXGAxgYCX6A) （提取码：1234）并将里面的文件到复制到手机内置存储

```shell
cp -r /sdcard/* /mnt  #将手机内置存储的所有文件复制到 /mnt目录
```

### 五、进入fastboot，启动uefi ，这里提供三种方法，推荐第一种

```shell
临时启动uefi
fastboot boot boot-polaris.img
刷入到当前boot分区
fastboot flash boot boot-polaris.img
刷入到recovery分区
fastboot flash recovery boot-polaris.img
```
