## awk

     
	 AWK是贝尔实验室1977年搞出来的文本出现神器.之所以叫AWK是因为其取了三位创始人
	 
	 Alfred Aho，Peter Weinberger, 和 Brian Kernighan 的Family Name的首字符。
	 
	 要学AWK，就得提一提AWK的一本相当经典的书《The AWK Programming Language》
	 
	 
	 
	 

- 1 输出

		
		awk '{print $1, $4}' netstat.txt
		
		# 输出第一列 和 第四列 
		
		单引号中的被大括号括着的就是awk的语句
		
		注意，其只能被单引号包含。
		
		其中的$1..$n表示第几例。
		
		注：$0表示整个行
		
		
		
		
		
	
- 2 输出

		
		awk '{printf "%-8s %-8s %-8s %-18s %-22s %-15s\n",$1,$2,$3,$4,$5,$6}' netstat.txt
		
		awk的格式化输出 和 C语言 printf 一样
		
		
		
	
	
- 3 过滤记录
	
		
		awk '$3==0 && $6=="LISTEN"' netstat.txt
		
		其中的“==”为比较运算符。其他比较运算符: != > >= < <=
		
		
		awk ' $3>0 {print $0}' netstat.txt
		
		内建变量 NR 记录数变量
		
		NR(Number of Record) 表示的是已经处理过的总记录数目
		
		或者说行号(不一定是一个文件，可能是多个)
		
		awk '$3==0 && $6=="LISTEN" || NR==1 '  netstat.txt
		
		awk '$3==0 && $6=="LISTEN" || NR==1 {printf "%-20s %-20s %s\n",$4,$5,$6}' netstat.txt
		
		
	
	
- 内建变量

		
		$0			当前记录（这个变量中存放着整个行的内容）
		
		$1~$n		当前记录的第n个字段，字段间由FS分隔
		
		FS			输入字段分隔符 默认是空格或Tab
		
		NF			当前记录中的字段个数，就是有多少列
		
		NR			已经读出的记录数，就是行号，从1开始，如果有多个文件话，这个值也是不断累加中
		
		FNR			当前记录数，与NR不同的是，这个值会是各个文件自己的行号
		
		RS			输入的记录分隔符， 默认为换行符
		
		OFS			输出字段分隔符， 默认也是空格
		
		ORS			输出的记录分隔符，默认为换行符
		
		FILENAME	当前输入文件的名字
		
		
		# 输出行号
		
		awk'$3==0 && $6=="ESTABLISHED" || NR==1 {printf "%02s %s %-20s %-20s %s\n",NR, FNR, $4,$5,$6}'netstat.txt
		
		# 指定分隔符
		
		awk 'BEGIN{FS=":"} {print $1,$3,$6}' /etc/passwd
		
		上面的命令也等价于：（-F的意思就是指定分隔符
		
		awk-F: '{print $1,$3,$6}'/etc/passwd
		
		如果你要指定多个分隔符 可以这样来：
		
		awk -F'[;:]'
		
		awk-F: '{print $1,$3,$6}'OFS="\t"/etc/passwd
		
	
	
- 字符串匹配

						
		awk '$6 ~ /FIN/ || NR==1 {print NR,$4,$5,$6}' OFS="\t" netstat.txt				
						
		awk '$6 ~ /WAIT/ || NR==1 {print NR,$4,$5,$6}' OFS="\t" netstat.txt
		
		上面的第一个示例匹配FIN状态
		
		第二个示例匹配WAIT字样的状态
		
		其实 ~ 表示模式开始
		
		/ /中是模式  这就是一个正则表达式的匹配。
		
		
		其实awk可以像grep一样的去匹配第一行，就像这样：
		
		awk '/LISTEN/' netstat.txt
		
		可以使用 “/FIN|TIME/” 来匹配 FIN 或者 TIME :
		
		awk'$6 ~ /FIN|TIME/ || NR==1 {print NR,$4,$5,$6}'OFS="\t" netstat.txt
		
		
		awk'$6 !~ /WAIT/ || NR==1 {print NR,$4,$5,$6}'OFS="\t"netstat.txt
		
		
		awk '!/WAIT/' netstat.txt 等于上面
						
						
	
	
- 折分文件

			
		awk拆分文件很简单，使用重定向就好了
		
		其中的NR!=1表示不处理表头
		
		awk 'NR!=1{print > $6}' netstat.txt	
			
		可以把指定的列输出到文件：
		
		awk 'NR!=1{print $4,$5 < $6}'netstat.txt
		
		再复杂一点：（注意其中的if-else-if语句，可见awk其实是个脚本解释器）
		
		awk 'NR!=1{if($6 ~ /TIME|ESTABLISHED/) print > "1.txt";
		
		else if($6 ~ /LISTEN/) print > "2.txt";
		
		else print > "3.txt" }' netstat.txt
		
			


- 统计

		
		awk 'NR!=1{a[$6]++;} END {for (i in a) print i ", " a[i];}' netstat.txt
		
		统计每个用户的进程的占了多少内存 注：sum的RSS那一列
		
		ps aux | awk 'NR!=1{a[$1]+=$6;} END { for(i in a) print i ", " a[i]"KB";}'
		
		
		END的意思是“处理完所有的行的标识”，即然说到了END就有必要介绍一下BEGIN
		
		这两个关键字意味着执行前和执行后的意思，语法如下：
		
		BEGIN{ 这里面放的是执行前的语句 }
		
		END {这里面放的是处理完所有的行后要执行的语句 }
		
		{这里面放的是处理每一行时要执行的语句}
		
		
		
		假设有这么一个文件（学生成绩表）：
		
		catscore.txt
		
		Marry	2143 78 84 77
		Jack	2321 66 78 45
		Tom		2122 48 77 71
		Mike	2537 87 97 95
		Bob		2415 40 57 62
		
		 
		awk脚本: cal.awk
		
		#!/bin/awk -f
		
		#运行前
		BEGIN {
		math = 0
		english = 0
		computer = 0
		printf"NAME NO. MATH ENGLISH COMPUTER TOTAL\n"
		printf"---------------------------------------------\n"
		}
		#运行中
		{
		math+=$3
		english+=$4
		computer+=$5
		printf"%-6s %-6s %4d %8d %8d %8d\n", $1, $2, $3,$4,$5, $3+$4+$5
		}
		#运行后
		END {
		printf"---------------------------------------------\n"
		printf" TOTAL:%10d %8d %8d \n", math, english, computer
		printf"AVERAGE:%10.2f %8.2f %8.2f\n", math/NR, english/NR, computer/NR
		}
		
		执行
		
		$ awk-f= cal.awk score.txt
		


- 环境变量

		
		即然说到了脚本，我们来看看怎么和环境变量交互
		
		使用-v参数和ENVIRON，使用ENVIRON的环境变量需要export
		
		$ x=5
		$ y=10
		$ export y
		$ echo$x $y
		5 10
		
		$ awk -v val=$x '{print $1, $2, $3, $4+val, $5+ENVIRON["y"]}' OFS="\t" score.txt
		
		Marry 2143 78 89 87
		Jack 2321 66 83 55
		Tom 2122 48 82 81
		Mike 2537 87 102 105
		Bob 2415 40 62 72
		
		#从file文件中找出长度大于80的行
		
		awk 'length>80' file
		
		#按连接数查看客户端IP
		
		netstat -ntu | awk'{print $5}'| cut-d: -f1 | sort| uniq-c | sort-nr
		
		#打印99乘法表
		
		Seq 9 | sed'H;g'| awk-vRS='''{for(i=1;i< NFiprintfdxd dsquot i NR iNR i="=NR?"\n":"\t")}'
		
		
		关于其中的一些知识点可以参看gawk的手册：
		
		内建变量 参看：http://www.gnu.org/software/gawk/manual/gawk.html#Built_002din-Variables
		
		流控方面 参看：http://www.gnu.org/software/gawk/manual/gawk.html#Statements
		
		内建函数 参看：http://www.gnu.org/software/gawk/manual/gawk.html#Built_002din
		
		正则表达式 参看：http://www.gnu.org/software/gawk/manual/gawk.html#Regexp
		
		
		

	