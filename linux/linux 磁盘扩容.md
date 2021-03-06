# Linux磁盘扩容

磁盘新扩容的空间，可以被独立创建成一个分区，或者用于扩容已有分区的空间。
Linux 虚拟机云硬盘扩容有以下两种场景需求：

1. 对于新增的容量空间，建立独立的新分区，老的分区保持不变。
2. 扩容旧的分区至新增的容量空间，并且保持老分区的数据不丢失。

以上两种场景，在您的Linux虚拟机云硬盘升级成功之后，都可以通过Linux下的分区扩容工具(fdisk/e2fsck/resize2fs)，执行一系列命令，完成分区扩容，并且保证原数据不会丢失。

以下分别介绍两种场景下的操作流程：

## 新空间格式化成一个独立分区

1. 查看数据盘信息
   登录Linux云服务器后，可以使用`fdisk -l`命令查看数据盘的已分区和未分区的信息。
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-16.png)

   使用`df –h`命令，无法看到未分区和格式化的数据盘。

![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-17.png)

2. 数据盘分区
   执行以下命令，新建一个分区

   ```
   fdisk /dev/xvdc
   ```

   按照界面的提示，依次输入”p”(查看现有分区信息)、“n”(新建分区)、“p”(新建主分区)、“2”(使用第2个主分区)，两次回车(使用默认配置)，输入“w”(保存分区表)，开始分区。
   这里是以创建1个分区为例，用户也可以根据自己的需求创建多个分区。
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-18.png)

3. 查看新分区
   使用`fdisk -l`命令，即可查看到，新的分区xvdc2已经创建完成
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-19.png)

4. 格式化新分区
   在进行分区格式化时，用户可以自行决定文件系统的格式，如ext2、ext3等。
   这里以“ext3”为例：
   使用下面的命令对新分区进行格式化。 
   `mkfs.ext3 /dev/ xvdc2`
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-20.png)

5. 挂载新分区
   使用命令`mkdir /data1`创建data1目录，再通过`mount /dev/xvdc2 /data1`命令手动挂载新分区后，用`df -h`命令查看，出现以下信息说明挂载成功，即可以查看到数据盘了。
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-21.png)

6. 添加分区信息
   如果希望云服务器在重启或开机时能自动挂载数据盘，必须将分区信息添加到/etc/fstab中。如果没有添加，则云服务器重启或开机后，都不能自动挂载数据盘。
   使用`echo '/dev/xvdc2 /data1 ext3 defaults 0 0' >> /etc/fstab`
   ”命令添加分区信息后，使用“cat /etc/fstab”命令查看，出现以下信息表示添加分区信息成功。
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-22.png)

## 新空间增加到已有分区空间中

1. 查看数据盘信息
   登录Linux云服务器后，可以使用`fdisk -l`命令查看数据盘的已分区和未分区的信息。使用`df –h`命令，无法看到未分区和格式化的数据盘。 

![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-23.png) 

2. 卸载正在使用的磁盘
   使用以下命令，卸载已挂载的需要扩容的磁盘
   `umount /data`
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-24.png)

3. 数据盘分区
   执行以下命令：
   `fdisk /dev/xvdc`
   按照界面的提示，一次输入”p”(查看已有分区信息), ”d”(删除需要扩容的分区)、“n”(新建分区)、“p”(新建主分区)、“1”(使用第1个主分区，必须保持与需要扩容的分区一样)，两次回车(使用默认配置)，输入“w”(保存分区表)，开始分区。
   这里是以创建1个分区为例，用户也可以根据自己的需求扩容多个分区。
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-25.png)

4. 检查扩容后的分区
   使用`fdisk –l /dev/xvdc1`命令，即可查看到，新的分区xvdc1已经创建并扩容完成
   再使用`e2fsck -f /dev/xvdc1`检查扩容的分区是否ok
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-26.png)

5. 扩容分区
   使用`resize2fs /dev/xvdc1`命令扩容分区
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-27.png)

6. 挂载扩容后的分区
   通过“mount /dev/xvdc1 /data”命令手动挂载扩容后的分区，用“df -h”命令查看，出现以下信息说明挂载成功，即可以查看到数据盘了。
   再执行”ll /data”命令，可以查看到，扩容后原分区的数据没有丢失
   ![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/yunyingpankuorong-28.png)

## 将新空间增加到已有分区空间中(第二中情况适用于磁盘未分区)

若原有的硬盘分区为一个MBR分区(可以看到vdb1,vdc1等字样)，同时在此分区上制作了文件系统。或原有的硬盘没有分区，直接在此硬盘上制作了文件系统。这两种情况都可以选择使用自动扩容工具进行扩容。

自动扩容工具适用于Linux操作系统，用于将扩容时新扩的云硬盘存储空间添加到已存在的文件系统中，扩容能够成功必须满足下面3个条件：

- 文件系统是ext2/ext3/ext4
- 当前文件系统不能有错误
- 扩容后的磁盘大小不超过2TB

下面介绍自动扩容工具的使用方法。

### 卸载正在使用的硬盘分区

执行以下命令卸载分区：

```
umount 挂载点
```

![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/image.jpg)

### 下载一键扩容工具

执行以下命令下载工具：

```
wget -O /tmp/devresize.py http://mirrors.tencentyun.com/install/virts/devresize.py
```

### 执行扩容工具

执行以下命令进行扩容：

```
python /tmp/devresize.py 硬盘路径
```

请注意，这里硬盘路径是需要扩容的云硬盘，而不是分区名。若您的文件系统在vdb1上，则应执行`python /tmp/devresize.py /dev/vdb`

![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/image.jpg)

若输出“The filesystem on /dev/vdb1 is now XXXXX blocks long.“则表示扩容成功。

若输出的是“[ERROR] - e2fsck failed!!“，请先用fsck对文件系统所在分区进行修复，可以执行以下命令进行自动修复:

```
fsck -a 分区路径
```

请注意这里与前一个命令不同，需要填写的是文件系统所在分区。若您的文件系统在vdb1上，则应执行`fsck -a /dev/vdb1`。

修复成功后，再使用`python /tmp/devresize.py 硬盘路径`来使用扩容工具进行扩容。

### 重新挂载扩容后的分区

执行以下命令挂载扩容后的分区：

```
mount 分区路径 挂载点
```

并通过以下命令查看扩容后的分区容量：

```
df -h
```

这里通过`mount /dev/vdb1 /data`命令手动挂载扩容后的分区(如果原先是没有分区的，执行`mount /dev/vdb /data`)，用`df -h`命令查看，出现以下信息说明挂载成功，即可以查看到数据盘了:

![img](linux%20%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9.assets/image.jpg)
再执行`ll /data`命令，可以查看到，扩容后原分区的数据没有丢失，新增加的存储空间已经扩容到文件系统中。