# RAID各级别特性小结
&ensp;&ensp;&ensp;&ensp;RAID(Redundant Arrays Independent Disks)译为独立冗余磁盘阵列，为了解决单块机械式硬盘有限的I/O能力，将多块硬盘由“控制器”组织起来做为一块硬盘使用，RAID通过多个磁盘并行读写和磁盘冗余提高磁盘的I/O能力和耐用性。RAID的实现方式有3种：
 1. 外接式磁盘阵列：通过扩展卡提供适配能力
 2. 内接式RAID：主板集成RAID控制器
     以上两种硬件式RAID配置接口为BOIS控制；
 3. Software RAID:通过内核模块mdadm实现

&ensp;&ensp;&ensp;&ensp;RAID实现时因多块磁盘组织在一起的工作方式不同，分为不同级别，常用的RAID级别有RAID-0，RAID-1，RAID-5, RAID-6， RAID-10等。RAID在进行数据存储时，由控制器将数据切割为相等大小的多个chunk，不同级别的存储chunk的方式不同，下面详细介绍RAID各级别的磁盘组织形式和特性。
## RAID-0 
 RAID-0将组成各磁盘空间对位条带化(strip)，由控制器切割的chunk依次均匀的存储在各组成磁盘的对位条带空间上；
 ![raid-0结构示图](https://baike.baidu.com/pic/%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97/1149823/0/ac4bd11373f08202e80699994bfbfbedaa641bfb?fr=lemma&ct=single)

 RAID-0特性：
 1. 最少磁盘数：2个
 2. 可用空间：所有组成磁盘中最小容量*磁盘个数
 3. 读、写性能均提升
 4. 无容错能力
## RAID-1
  RAID-1将组成各磁盘空间对位条带化(strip)，每个磁盘对位存储所有chunk，两个磁盘间相互镜像，所以RAID1也称为镜像卷；
![raid-1结构图示](http://img2.ph.126.net/WHLjNecalQaUBCsQtP8Xzg==/4886405595797136491.jpg)

RAID-1特性：
 1. 最少磁盘数：2个
 2. 可用空间：所有组成磁盘中最小容量*磁盘个数/2
 3. 读性能提升/写性能有所下降
 4. 有容错能力，可以坏一块磁盘
## RAID-4
RAID-4至少要3块磁盘一组，将组成的各磁盘的空间对位条带化(strip)，其中两块磁盘DISK1和DISK2依次均匀的存储chunk，DISK3对位存放DISK1和DISK2对位条带上数据异或运算的值；这样3块磁盘中任何一块损坏，其上的数据可有其它两块磁盘上的数据计算而得，可以添加一块备用磁盘，任何一块磁盘损坏，备用磁盘自动启动，并将由其它两块磁盘的运算结果将数据恢复；
RAID-4特性：
 1. 最少磁盘数：3个
 2. 可用空间：（N-1）*最小磁盘容量
 3. 读、写性能均提升
 4. 有容错能力
## RAID-5
RAID-4将数据的校验码存放在一块磁盘上，存放校验码的磁盘由于访问压力较大，所以更容易损坏；RAID-5改善了这一情况。RAID-5也是3块磁盘一组，将组成的各磁盘的空间对位条带化(strip)，chunk和校验码在3块磁盘的对位上轮流存放，取消了固定存放校验码的磁盘，这样访问压力在3块磁盘上分散，进一步提高了耐用性
![raid-5 结构图示](http://img0.ph.126.net/ammZiNuefZhY73GI1pKo7Q==/1460292179275235937.jpg)


RAID-5特性：
 1. 最少磁盘数：3个
 2. 可用空间：（N-1）*最小磁盘容量
 3. 读、写性能均提升
 4. 有容错能力
## RAID-6
RAID-6在RAID的基础上增加了一块磁盘，存放两份校验码，这样进一步提升了磁盘的容物性，所以RAID-6至少需4块磁盘。
RAID-6特性：
 1. 最少磁盘数：4个
 2. 可用空间：（N-2）*最小磁盘容量
 3. 读、写性能均提升
 4. 有容错能力
## RAID-10
RAID-10是将磁盘先两两分组按RAID-1方式组织，然后每组再按RAID-0组织；以4块磁盘为例说明，由控制器分割的相等size的chunk先平均分配给RAID-0的两个组成部分，这两部分按RAID-1方式将数据在两个磁盘上镜像存储，这样每组RAID-1只能坏一块设备

RAID-10特性：
1. 最少磁盘数：4个
 2. 可用空间：根据组织方式不同而不同
 3. 读、写性能均提升
 4. 有容错能力

以上即为常用的各RAID级别的磁盘组织形式和特性介绍。