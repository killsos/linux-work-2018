## linux服务器的性能分析与优化

#### 影响Linux服务器性能的因素

- CPU

		
		目前大部分CPU在同一时间只能运行一个线程，超线程的处理器可以在同一时间处理多个线程，
		
		因此可以利用超线程特性提高系统性能。
		
		在linux系统下只有运行SMP内核才能支持超线程，但是安装的CPU数量越多，从超线程获得的性能提升越少。
		
		另外linux内核会将多核的处理器当做多个单独的CPU来识别，
		
		例如，两个4核的CPU会被当成8个单个CPU，从性能角度讲，两个4核的CPU整体性能要比8个单核CPU低25%-30%
		
		可能出现CPU瓶颈的应用有邮件服务器、动态web服务器等。
		
		
		SMP
		
			SMP的全称是“对称多处理“（Symmetrical Multi-Processing）技术，
			
			是指在一个计算机上汇集了一组处理器(多CPU),各CPU之间共享内存子系统以及总线结构。
			
			它是相对非对称多处理技术而言的、应用十分广泛的并行技术。
			
			在这种架构中，一台电脑不再由单个CPU组成，而同时由多个处理器运行操作系统的单一复本，
			
			并共享内存和一台计算机的其他资源。虽然同时使用多个CPU，但是从管理的角度来看，它们的表现就像一台单机一样。
			
			系统将任务队列对称地分布于多个CPU之上，从而极大地提高了整个系统的数据处理能力。
			
			所有的处理器都可以平等地访问内存、I/O和外部中断。在对称多处理系统中，系统资源被系统中所有CPU共享，
			
			工作负载能够均匀地分配到所有可用处理器之上。
		
		
		


- 内存

							
		内存太小，系统进程将被阻塞，应用也将变得缓慢，甚至失去响应；内存太大，导致资源浪费。
		
		虚拟内存可以缓解物理内存的不足，但是虚拟内存的过多占用会导致应用程序的性能明显下降。
		
		在一个32位处理器的linux系统中超过8GB的物理内存都将被浪费，因此要使用更大的内存，
		
		建议安装64位的操作系统，同时开启linux的大内存内核支持。
		
		由于处理器寻址范围的限制，在32位linux操作系统上，应用程序单个进程最大只能使用2GB的内存。
		
		可能出现内存瓶颈的有打印服务器、数据库服务器、静态web服务器等					
							
							
	
	
- 磁盘I/O性能

		
		磁盘RAID技术，Redundant Array of Independent Disk，即独立磁盘冗余阵列，简称磁盘阵列。
		
		RAID通过将多块独立的磁盘（物理硬盘）按不同的方式组合起来形成一个磁盘组（逻辑硬盘），
		
		从而提供比单个磁盘更高的IO性能和数据冗余。
		
		根据磁盘组合方式不同，
		
		分为RAID0、RAID1、RAID2、RAID3、RAID4、RAID5、RAID6、RAID7、RAID0+1、RAID10等，
		
		常用的有RAID0、RAID1、RAID5、RAID0+1。
		
		RAID0：通过把多块硬盘粘合成一个容量更大的硬盘组，
		
		提高磁盘的性能和吞吐量，成本低，至少两块磁盘，
		
		但是没有容错和数据修复功能，只能用在对数据安全性要求不高的环境中。
		
		RAID1：也就是磁盘镜像，通过把一个磁盘的数据镜像到另外一个磁盘上，
		
		最大限度的保证磁盘数据的可靠性和可修改性，具有很高的数据冗余能力，
		
		但是磁盘利用率只有50%，成本较高，多用在保存重要数据的场合。
		
		RAID5：磁盘分段加奇偶校验技术，提高了系统的可靠性。
		
		读出效率很高，写效率一般，至少需要3块磁盘，允许一块磁盘故障，不影响数据的可用性。
		
		RAID0+1：把RAID0和RAID1技术结合起来，至少需要4块硬盘，
		
		每个盘都有其镜像盘，提高全冗余能力，并具有快速读写能力。
		
		
		
		
	
	
- 网络带宽

		
		
		
		
		
	
	
#### 系统性能评估标准  



![alt text](./01.png "Title")


							
	%user：表示CPU处在用户模式下的时间百分比。
	
	%sys：表示CPU处在系统模式下的时间百分比。
	
	%iowait：表示CPU等待输入输出完成时间的百分比。
	
	swap in：即si，表示虚拟内存的页导入，即从SWAP DISK交换到RAM
	
	swap out：即so，表示虚拟内存的页导出，即从RAM交换到SWAP DISK。
							
							
							
							


#### 系统性能分析工具 

				
	常用系统命令
	
	Vmstat、sar、iostat、netstat、free、ps、top等
	
	常用组合方式
	
	用vmstat、sar、iostat检测是否是CPU瓶颈
	
	用free、vmstat检测是否是内存瓶颈
	
	用iostat检测是否是磁盘I/O瓶颈
	
	用netstat检测是否是网络带宽瓶颈
				
				
				


- 系统整体性能评估（uptime命令）

				
				
		# uptime
		
		16:38:00 up 118 days,  3:01,  5 users,  load average: 1.22, 1.02, 0.91
				
		这里需要注意的是：load average这个输出值，这三个值的大小一般不能大于系统CPU的个数，
		
		例如，本输出中系统有8个CPU,如果load average的三个值长期大于8时，
		
		说明CPU很繁忙，负载很高，可能会影响系统性能，但是偶尔大于8时，倒不用担心，一般不会影响系统性能。
		
		相反，如果load average的输出值小于CPU的个数，
		
		则表示CPU还有空闲的时间片，比如本例中的输出，CPU是非常空闲的。
				
		load average 三个数值 对应 5分钟 10分钟  15分钟
				
				
	
- cpu性能评估

			
		利用vmstat命令监控系统CPU
		
			该命令可以显示关于系统各种资源之间相关性能的简要信息，这里我们主要用它来看CPU一个负载情况	
			
			下面是vmstat命令在某个系统的输出结果：
		
			# vmstat 2 3    #每2秒更新信息，统计3次
		
		procs -----------memory----------  ---swap--  -----io---- --system--  -----cpu------
		
		 r  b   swpd   free      buff  cache   si   so    bi    bo       in     cs     us sy  id   wa st
		 0  0    0    162240     8304  67032   0    0     13    21       1007   23     0  1   98   0   0
		 0  0    0    162240     8304  67032   0    0     1     0        1010   20     0  1   100  0   0
		 0  0    0    162240     8304  67032   0    0     1     1        1009   18     0  1   99   0   0
		 
		 
		 Procs
		 
		 	r列表示运行和等待cpu时间片的进程数，这个值如果长期大于系统CPU的个数，说明CPU不足，需要增加CPU。
			
		 	b列表示在等待资源的进程数，比如正在等待I/O、或者内存交换等。
			
		 Cpu
		 
		 	us列显示了用户进程消耗的CPU 时间百分比。us的值比较高时，说明用户进程消耗的cpu时间多，但是如果长期大于50%，就需要考虑优化程序或算法。
			
		 	sy列显示了内核进程消耗的CPU时间百分比。Sy的值较高时，说明内核消耗的CPU资源很多。
			
		    根据经验，us+sy的参考值为80%，如果us+sy大于 80%说明可能存在CPU资源不足
			
		 
		 利用sar命令监控系统CPU 
		 
		  		sar功能很强大，可以对系统的每个方面进行单独的统计，但是使用sar命令会增加系统开销，
				
				不过这些开销是可以评估的，对系统的统计结果不会有很大影响。
		 
		下面是sar命令对某个系统的CPU统计输出：
		
		# sar -u 3 5 #u显示系统所有cpu在采样时间内的负载状态
		
		Linux 2.6.9-42.ELsmp (webserver)        11/28/2008      _i686_  (8 CPU)
		
		11:41:24 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
		11:41:27 AM     all      0.88      0.00      0.29      0.00      0.00     98.83
		11:41:30 AM     all      0.13      0.00      0.17      0.21      0.00     99.50
		11:41:33 AM     all      0.04      0.00      0.04      0.00      0.00     99.92
		11:41:36 AM     all      90.08     0.00      0.13      0.16      0.00     9.63
		11:41:39 AM     all      0.38      0.00      0.17      0.04      0.00     99.41
		
		Average:        all      0.34      0.00      0.16      0.05      0.00     99.45
		
		

- 内存性能评估

		
		利用free指令监控内存
		
		free是监控linux内存使用状况最常用的指令，看下面的一个输出
		
		free  -m  #查看以M为单位的内存使用情况
		
				   total         used       free     shared    buffers     cached
		Mem:       8111          7185        926          0    243           6299
		
		-/+ buffers/cache:     643       7468
		
		Swap:       8189          0         8189
		
		一般有这样一个经验公式：应用程序可用内存/系统物理内存>70%时，表示系统内存资源非常充足，
		
		不影响系统性能，应用程序可用内存/系统物理内存<20%时，表示系统内存资源紧缺，
		
		需要增加系统内存，20%<应用程序可用内存/系统物理内存<70%时，
		
		表示系统内存资源基本能满足应用需求，暂时不影响系统性能。 
		
		
		 一般有这样一个经验公式：应用程序可用内存/系统物理内存>70%时，表示系统内存资源非常充足，
		 
		 不影响系统性能，应用程序可用内存/系统物理内存<20%时，表示系统内存资源紧缺，需要增加系统内存，
		 
		 20%<应用程序可用内存/系统物理内存<70%时，表示系统内存资源基本能满足应用需求，暂时不影响系统性能。 
			
		
		
		利用vmstat命令监控内存 
		
		vmstat 2 3
		
		procs -----------memory----------  ---swap--------io---- --system---------cpu------
		 r  b   swpd   free      buff  cache   si   so    bi    bo       in     cs     us sy  id  wa st
		 0  0    0    162240   8304  67032   0    0    13    21   1007   23     0  1  98   0  0
		 0  0    0    162240   8304  67032   0    0     1     0     1010   20     0  1  100 0  0
		 0  0    0    162240   8304  67032   0    0     1     1     1009   18     0  1  99   0  0
		
		memory
		
			swpd列表示切换到内存交换区的内存数量（以k为单位）。如果swpd的值不为0，或者比较大，
			
			只要si、so的值长期为0，这种情况下一般不用担心，不会影响系统性能。
			
			free列表示当前空闲的物理内存数量（以k为单位）
			
			buff列表示buffers cache的内存数量，一般对块设备的读写才需要缓冲。 
			
			cache列表示page cached的内存数量，一般作为文件系统cached，
			
			频繁访问的文件都会被cached，如果cache值较大，说明cached的文件数较多，
			
			如果此时IO中bi比较小，说明文件系统效率比较好。
			
		swap
		
			si列表示由磁盘调入内存，也就是内存进入内存交换区的数量。
		
			so列表示由内存调入磁盘，也就是内存交换区进入内存的数量。
		 
			一般情况下，si、so的值都为0，如果si、so的值长期不为0，则表示系统内存不足。需要增加系统内存。
		
		
	
- 磁盘I/O性能评估 

			
		磁盘存储基础
			
			熟悉RAID存储方式，可以根据应用的不同，选择不同的RAID方式。
			
			尽可能用内存的读写代替直接磁盘I/O，使频繁访问的文件或数据放入内存中进行操作处理，
			
			因为内存读写操作比直接磁盘读写的效率要高千倍。
			
			将经常进行读写的文件与长期不变的文件独立出来，分别放置到不同的磁盘设备上。
			
			对于写操作频繁的数据，可以考虑使用裸设备代替文件系统。
		
		使用裸设备的优点有：
			
			数据可以直接读写，不需要经过操作系统级的缓存，节省了内存资源，避免了内存资源争用。
			
			避免了文件系统级的维护开销，比如文件系统需要维护超级块、I-node等。
			
			避免了操作系统的cache预读功能，减少了I/O请求。
			
		使用裸设备的缺点是：
			
			数据管理、空间管理不灵活，需要很专业的人来操作。
			
			
		利用iostat评估磁盘性能
			
			iostat -d 2 3  #-d 显示磁盘的使用情况
			
		Linux 2.6.9-42.ELsmp (webserver)        12/01/2008      _i686_  (8 CPU)
		 
		Device:         tps   Blk_read/s   Blk_wrtn/s   Blk_read      Blk_wrtn
		sda               1.87         2.58       114.12        6479462     286537372
		 
		Device:         tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
		sda               0.00         0.00         0.00              0                0
		 
		Device:         tps   Blk_read/s   Blk_wrtn/s   Blk_read    Blk_wrtn
		sda               1.00         0.00        12.00             0                24
		
		
		对上面每项的输出解释如下：
			
			Blk_read/s表示每秒读取的数据块数。
			
			Blk_wrtn/s表示每秒写入的数据块数。
			
			Blk_read表示读取的所有块数。
			
			Blk_wrtn表示写入的所有块数。
			
			可以通过Blk_read/s和Blk_wrtn/s的值对磁盘的读写性能有一个基本的了解，
			
			如果Blk_wrtn/s值很大，表示磁盘的写操作很频繁，可以考虑优化磁盘或者优化程序
			
			如果Blk_read/s值很大，表示磁盘直接读取操作很多，可以将读取的数据放入内存中进行操作。
			
			对于这两个选项的值没有一个固定的大小，根据系统应用的不同，会有不同的值，
			
			但是有一个规则还是可以遵循的：长期的、超大的数据读写，肯定是不正常的，这种情况一定会影响系统性能。
			
		
		
		
		利用sar评估磁盘性能
			
			通过“sar –d”组合，可以对系统的磁盘IO做一个基本的统计，请看下面的一个输出
			
		[root@webserver ~]# sar -d 2 3
		Linux 2.6.9-42.ELsmp (webserver)        11/30/2008      _i686_  (8 CPU)
		 
		11:09:33 PM  DEV     tps   rd_sec/s   wr_sec/s  avgrq-sz  avgqu-sz   await  svctm   %util
		11:09:35 PM dev8-0  0.00  0.00            0.00        0.00          0.00         0.00   0.00     0.00
		 
		11:09:35 PM  DEV     tps  rd_sec/s    wr_sec/s  avgrq-sz  avgqu-sz  await   svctm   %util
		11:09:37 PM dev8-0  1.00  0.00         12.00        12.00         0.00        0.00    0.00     0.00
		 
		11:09:37 PM   DEV    tps    rd_sec/s  wr_sec/s   avgrq-sz  avgqu-sz  await  svctm   %util
		11:09:39 PM dev8-0  1.99   0.00         47.76         24.00       0.00        0.50    0.25     0.05
		 
		Average:  DEV          tps    rd_sec/s   wr_sec/s  avgrq-sz  avgqu-sz    await  svctm   %util
		Average:  dev8-0      1.00   0.00          19.97         20.00       0.00         0.33    0.17     0.02
		
		
		
		需要关注的几个参数含义：
		
			await表示平均每次设备I/O操作的等待时间（以毫秒为单位）
			
			svctm表示平均每次设备I/O操作的服务时间（以毫秒为单位）
			
			%util表示一秒中有百分之几的时间用于I/O操作
			
		对以磁盘IO性能，一般有如下评判标准：
		
			正常情况下svctm应该是小于await值的，
			
			而svctm的大小和磁盘性能有关，CPU、内存的负荷也会对svctm值造成影响，
			
			过多的请求也会间接的导致svctm值的增加。
			
			await值的大小一般取决与svctm的值和I/O队列长度以及I/O请求模式，
			
			如果svctm的值与await很接近，表示几乎没有I/O等待，磁盘性能很好，
			
			如果await的值远高于svctm的值，则表示I/O队列等待太长，
			
			系统上运行的应用程序将变慢，此时可以通过更换更快的硬盘来解决问题。
			
			%util项的值也是衡量磁盘I/O的一个重要指标，如果%util接近100%，
			
			表示磁盘产生的I/O请求太多，I/O系统已经满负荷的在工作，该磁盘可能存在瓶颈。
			
			长期下去，势必影响系统的性能，可以通过优化程序或者通过更换更高、更快的磁盘来解决此问题。
		
			


- 网络性能评估

			
		1 通过ping命令检测网络的连通性
		
		2 通过netstat –i组合检测网络接口状况
		
		3 通过netstat –r组合检测系统的路由表信息
		
		4 通过sar –n组合显示系统的网络运行状态  sar -n DEV 5 3
			
			


- 常用分析

			
		netstat -an|awk '/^tcp/{++S[$NF]}END{for (a in S)print a,S[a]}'   查看tcp链接数
		
		netstat -pant |grep ":80"|awk '{print $5}' | awk -F: '{print $1}'|sort|uniq -c|sort -nr    查看连接数最多的ip
		
		cat access.log|awk '{print $1}'|sort|uniq -c|sort -nr|head -n10
		
		提取日志  分别是访问URL和URL访问来源  排序
		
		awk '{print $7}' access.log | sort | uniq -c |sort -nr | head -n10 > test.txt
		

- shell分析nginx日志

		
		178.255.215.86 - - [04/Jul/2013:00:00:31 +0800] "GET /tag/316/PostgreSQL HTTP/1.1" 200 4779 "-" "Mozilla/5.0 
		
		(compatible; Exabot/3.0 (BiggerBetter); +http://www.exabot.com/go/robot)" "-"- 178.255.215.86 - - 
		
		[04/Jul/2013:00:00:34 +0800] "GET /tag/317/edit HTTP/1.1" 303 5 "-" "Mozilla/5.0 
		
		(compatible; Exabot/3.0 (BiggerBetter); +http://www.exabot.com/go/robot)" "-"- 
		
		103.29.134.200 - - [04/Jul/2013:00:00:34 +0800] "GET /code-snippet/2022/edit 
		
		HTTP/1.0" 303 0 "-" "Mozilla/5.0 (Windows NT 6.1; rv:17.0) Gecko/17.0 Firefox/17.0" "-"- 103.29.134.200
		
		 - - [04/Jul/2013:00:00:35 +0800] "GET /user/login?url=http%3A//outofmemory.cn/code-snippet/2022/edit 
		 
		 HTTP/1.0" 200 4748 "-" "Mozilla/5.0 (Windows NT 6.1; rv:17.0) Gecko/17.0 Firefox/17.0" "-"-
		
		以下脚本都是基于上面日志格式的，如果你的日志格式不同需要调整awk后面的参数。
		
		分析日志中的UserAgent
		
		cat access_20130704.log | awk -F "\"" '{print $(NF-3)}' | sort | uniq -c | sort -nr | head -20
		
		上面的脚本将分析出日志文件中最多的20个UserAgent
		
		
		分析日志中那些IP访问最多
		
		cat access_20130704.log | awk '{print $1}' | sort | uniq -c | sort -nr | head -20
		
		分析日志中那些Url请求访问次数最多
		
		cat access_20130704.log | awk -F "\"" '{print $(NF-5)}' | sort | uniq -c | sort -nr | head -20 
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
	