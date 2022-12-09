# Linux指令

## 一、登录与注销

1)	sudo useradd lilei  //添加用户 (不能被立即使用，需设置密码 sudo passwd lilei)  
2)	sudo adduser lilei  //添加用户
3)	login  //登录或切换用户
4)	logout //注销用户（命令行）  exit(shell-退出控制台)
5)	shutdown -h 10  //10分钟后自动关机	shutdown -c  //取消
6)	halt(root用户)  //关闭所有进程后自动关机
7)	poweroff //同上
8)	shutdown -r 10 //十分钟后自动重启
9)	init 6  //重启 （0-停机，1-单用户，2-多用户，3-完全多用户，4-图形化，5-安全模式，6-重启）
10)	reboot  //重启



## 二、系统信息 

1. arch 显示机器的处理器架构(1) 
2. uname -m 显示机器的处理器架构(2) 
3. uname -r 显示正在使用的内核版本 
4. dmidecode -q 显示硬件系统部件 - (SMBIOS / DMI) 
5. hdparm -i /dev/hda 罗列一个磁盘的架构特性 
6. hdparm -tT /dev/sda 在磁盘上执行测试性读取操作 
7. cat /proc/cpuinfo 显示CPU info的信息 
8. cat /proc/interrupts 显示中断 
9. cat /proc/meminfo 校验内存使用 
10. cat /proc/swaps 显示哪些swap被使用 
11. cat /proc/version 显示内核的版本 
12. cat /proc/net/dev 显示网络适配器及统计 
13. cat /proc/mounts 显示已加载的文件系统 
14. lspci -tv 罗列 PCI 设备 
15. lsusb -tv 显示 USB 设备 
16. date 显示系统日期 

## 三、获取帮助

1)	man ls  //获取帮助
2)	man -k ls  //不清楚完整名字
3)	whatis ls  //获取帮助
4)	help cd  / cd –help  //获取帮助 -d(简短描述) -s(用法简介)
5)	info who  //获取帮助



## 四、文件和目录 

1)	pwd   //显示当前工作目录
2)	mkdir mydir  //创建工作目录
3)	cd mydir  //更改工作目录
4)	rmdir mydir //删除工作目录
5)	touch myfile  //创建文件
6)	mv myfile mydir  //移动目录或文件
7)	cp myfile myfir  //复制目录或文件
8)	rm -rf mydir  //删除目录或文件
9)	ls -l myfile  //查看文件最后被编辑时间
10)	ls -lu myfile //查看文件最后被访问时间
11)	touch -at 01011212 myfile  //修改文件最后被访问时间
12)	ls //列出所有文件和目录
13)	ls -a //查看所有文件
14)	ls -i //显示文件索引节点号
15)	ls -l //详细显示
16)	ls -m //以逗号分隔
17)	sudo apt-get install tree 
18)	tree -l//以树状图列出目录内容
19)	tree -a //所有
20)	tree -i //不以阶梯状
21)	tree -s  //列出文件或目录大小
22)	tree -t  //按更改时间
23)	file -b myfile  //显示目录或文件的详细信息
24)	stat myfile  //同上



## 五、文件搜索 

1. find / -name file1 从 '/' 开始进入根文件系统搜索文件和目录 
   find / -user user1 搜索属于用户 'user1' 的文件和目录 
   find /home/user1 -name \*.bin 在目录 '/ home/user1' 中搜索带有'.bin' 结尾的文件 
   find /usr/bin -type f -atime +100 搜索在过去100天内未被使用过的执行文件 
   find /usr/bin -type f -mtime -10 搜索在10天内被创建或者修改过的文件 
   find / -name \*.rpm -exec chmod 755 '{}' \; 搜索以 '.rpm' 结尾的文件并定义其权限 ^                       
2.  find /tmp -name \*.hprof -exec rm -f {} \;批量删除java堆栈.hprof文件
   find / -xdev -name \*.rpm 搜索以 '.rpm' 结尾的文件，忽略光驱、捷盘等可移动设备 
   locate \*.ps 寻找以 '.ps' 结尾的文件 - 先运行 'updatedb' 命令 
   whereis halt 显示一个二进制文件、源码或man的位置 
   which halt 显示一个二进制文件或可执行文件的完整路径 
3. grep -rn "query_string" *  Linux目录下全局查找所有文件中是否包含指定字符串（-r：递归；-n：显示行号）


## 六、挂载一个文件系统 

1. mount /dev/hda2 /mnt/hda2 挂载一个叫做hda2的盘 - 确定目录 '/ mnt/hda2' 已经存在 

2. umount /dev/hda2 卸载一个叫做hda2的盘 - 先从挂载点 '/ mnt/hda2' 退出 
3. fuser -km /mnt/hda2 当设备繁忙时强制卸载 
4. umount -n /mnt/hda2 运行卸载操作而不写入 /etc/mtab 文件- 当文件为只读或当磁盘写满时非常有用 
5. mount /dev/fd0 /mnt/floppy 挂载一个软盘 
6. mount /dev/cdrom /mnt/cdrom 挂载一个cdrom或dvdrom 
7. mount /dev/hdc /mnt/cdrecorder 挂载一个cdrw或dvdrom 
8. mount /dev/hdb /mnt/cdrecorder 挂载一个cdrw或dvdrom 
9. mount -o loop file.iso /mnt/cdrom 挂载一个文件或ISO镜像文件 
10. mount -t vfat /dev/hda5 /mnt/hda5 挂载一个Windows FAT32文件系统 
11. mount /dev/sda1 /mnt/usbdisk 挂载一个usb 捷盘或闪存设备 
12. mount -t smbfs -o username=user,password=pass //WinClient/share /mnt/share 挂载一个windows网络共享 

 

## 七、磁盘空间 

df -h 显示已经挂载的分区列表 

ls -lSr |more 以尺寸大小排列文件和目录 

du -sh dir1 估算目录 'dir1' 已经使用的磁盘空间' 

du -sk * | sort -rn 以容量大小为依据依次显示文件和目录的大小 

rpm -q -a --qf '%10{SIZE}t%{NAME}n' | sort -k1,1n 以大小为依据依次显示已安装的rpm包所使用的空间 (fedora, redhat类系统) 

dpkg-query -W -f='${Installed-Size;10}t${Package}n' | sort -k1,1n 以大小为依据显示已安装的deb包所使用的空间 (ubuntu, debian类系统) 

hdfs dfs -du /dw/default | sort -rn | head -n 10 | awk '{printf("%.2f\t\t%.2f\t\t%s\t\n",$1/1024/1024/1024,"\t"$2/1024/1024/1024,"\t"$3)}'  查询hdfs文件系统中表文件大小，按从大到小的顺序排列(取前10列)，单位GB

## 八、系统负载 -- top 

1. top -d 20 -p 1303                             将进程号1303的系统负载，每隔20秒刷新一次。英文状态下，按住c键，将展示进行的详细环境信息，对于java程序调试来说，非常友好。 
   top -d 20 -n 3 -b > test.txt                  每隔20秒，一共执行3次， 将统计结果导入到test.txt文件中。           
2. top命令显示不全，添加-w参数：
3. 命令为：
   top -b -n 1
   -b为 批处理模式，-n为刷新的次数
   发现信息显示不全，最后man top，加一个参数w后，完全显示
4. top -b -n 1 -w 512
   如果需要显示完整的COMMAND命令，使用top -c参数

5. top -c -bw 500
   查看完整进程名， 按500个字符长度查看（这样基本可以查看到完整的命令）



## 九、用户和群组 

1. groupadd group_name 创建一个新用户组 
2. groupdel group_name 删除一个用户组 
3. groupmod -n new_group_name old_group_name 重命名一个用户组 
4. useradd -c "Name Surname " -g admin -d /home/user1 -s /bin/bash user1 创建一个属于 "admin" 用户组的用户 
5. useradd user1 创建一个新用户 
6. userdel -r user1 删除一个用户 ( '-r' 排除主目录) 
7. usermod -c "User FTP" -g system -d /ftp/user1 -s /bin/nologin user1 修改用户属性 
8. passwd 修改口令 
9. passwd user1 修改一个用户的口令 (只允许root执行) 
10. chage -E 2005-12-31 user1 设置用户口令的失效期限 
11. pwck 检查 '/etc/passwd' 的文件格式和语法修正以及存在的用户 
12. grpck 检查 '/etc/passwd' 的文件格式和语法修正以及存在的群组 
13. newgrp group_name 登陆进一个新的群组以改变新创建文件的预设群组 



## 十、文件的权限 

- ls -lh 显示权限 
- ls /tmp | pr -T5 -W$COLUMNS 将终端划分成5栏显示 
- chmod ugo+rwx directory1 设置目录的所有人(u)、群组(g)以及其他人(o)以读（r ）、写(w)和执行(x)的权限 
- chmod go-rwx directory1 删除群组(g)与其他人(o)对目录的读写执行权限 
- chown user1 file1 改变一个文件的所有人属性 
- chown -R user1 directory1 改变一个目录的所有人属性并同时改变改目录下所有文件的属性 
- chgrp group1 file1 改变文件的群组 
- chown user1:group1 file1 改变一个文件的所有人和群组属性 
- find / -perm -u+s 罗列一个系统中所有使用了SUID控制的文件 
- chmod u+s /bin/file1 设置一个二进制文件的 SUID 位 - 运行该文件的用户也被赋予和所有者同样的权限 
- chmod u-s /bin/file1 禁用一个二进制文件的 SUID位 
- chmod g+s /home/public 设置一个目录的SGID 位 - 类似SUID ，不过这是针对目录的 
- chmod g-s /home/public 禁用一个目录的 SGID 位 
- chmod o+t /home/public 设置一个文件的 STIKY 位 - 只允许合法所有人删除文件 
- chmod o-t /home/public 禁用一个目录的 STIKY 位 

## 十一、文件的特殊属性 

1. chattr +a file1 只允许以追加方式读写文件 
2. chattr +c file1 允许这个文件能被内核自动压缩/解压 
3. chattr +d file1 在进行文件系统备份时，dump程序将忽略这个文件 
4. chattr +i file1 设置成不可变的文件，不能被删除、修改、重命名或者链接 
5. chattr +s file1 允许一个文件被安全地删除 
6. chattr +S file1 一旦应用程序对这个文件执行了写操作，使系统立刻把修改的结果写到磁盘 
7. chattr +u file1 若文件被删除，系统会允许你在以后恢复这个被删除的文件 
8. lsattr 显示特殊的属性 

## 十二、打包和压缩文件 

- bunzip2 file1.bz2 解压一个叫做 'file1.bz2'的文件 
- bzip2 file1 压缩一个叫做 'file1' 的文件 
- gunzip file1.gz 解压一个叫做 'file1.gz'的文件 
- gzip file1 压缩一个叫做 'file1'的文件 
- gzip -9 file1 最大程度压缩 
- rar a file1.rar test_file 创建一个叫做 'file1.rar' 的包 
- rar a file1.rar file1 file2 dir1 同时压缩 'file1', 'file2' 以及目录 'dir1' 
- unrar x file1.rar 解压rar包   #如果无unrar命令，参考：Linux CentOS 7.0 下 rar unrar的安装
- tar -cvf archive.tar file1 创建一个非压缩的 tarball 
- tar -cvf archive.tar file1 file2 dir1 创建一个包含了 'file1', 'file2' 以及 'dir1'的档案文件 
- tar -tf archive.tar 显示一个包中的内容 
- tar -xvf archive.tar 释放一个包 
- tar -xvf archive.tar -C /tmp 将压缩包释放到 /tmp目录下 
- tar -cvfj archive.tar.bz2 dir1 创建一个bzip2格式的压缩包 
- tar -jxvf archive.tar.bz2 解压一个bzip2格式的压缩包 
- tar -cvfz archive.tar.gz dir1 创建一个gzip格式的压缩包 
- tar -zxvf archive.tar.gz 解压一个gzip格式的压缩包 
- zip file1.zip file1 创建一个zip格式的压缩包 
- zip -r file1.zip file1 file2 dir1 将几个文件和目录同时压缩成一个zip格式的压缩包 
- unzip file1.zip 解压一个zip格式压缩包 

