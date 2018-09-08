## 安装mysql

	
	1. yum install -y mariadb mariadb-server
	
	2. 为了保证 systemctl 运行
		
		setenforce 0
		
	3. 将mariadb自启 启动
	
		systemctl start mariadb #启动服务
		
		systemctl enable mariadb #设置开机启动
		
		systemctl restart mariadb #重新启动
		
		systemctl stop mariadb.service #停止MariaDB
	
	5 mariadb初始化
		
		mysql_secure_installation
	
	6 登录
		
		mysql -uroot -p
	
	
	
	


## 安装apache

	
	1. yum install -y httpd
	
	2. 启动apache
		
		systemctl enable httpd
		
		systemctl start  httpd
	
	3. 查看80
		
		netstat -antp
		
	4. apache的web存放目录
		
		/var/www/html
		
	5. apache的配置文件
		
		/etc/httpd/conf/httpd.conf
	
	
	
	
	


## 安装php

	
	1. yum -y install php
	
	2. 重启httpd服务激活php  为了httpd加载php-module
		
	   systemctl restart httpd
	
	3 .测试php是否安装完成
		
		vim /var/www/html/info.php
		
		<?ph>
			phpinfo();
		?>
	
	4. 安装php的组件
		
		搜索php组件
		
			yum search php
			
		安装常用组件
		
			yum -y install php-mysql php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc  php-bcmath php-mbstring
		
		记得安装完要重启httpd服务激活组件
			
			systemctl restar thttpd 
		
		php配置文件
			
			vim /etc/php.ini
			
			memory_limit=128M
			
			post_max_size=8M
			
			upload_max_filesize=2M
			
			
			
	con.php
		
		<?php
			$con = mysql_connect("localhost","root","123");
		
			#echo $con;
		
			mysql_select_db("a123", $con);
		
			$result = mysql_query("SELECT * FROM a");
		
			while($row = mysql_fetch_array($result))
		  	{
		        echo $row['name'] . " " . $row['age'];
		        echo "<br />";
		  	}
		  
			mysql_close($con);	
		?>
		
	



- 老的
	
		
		什么是LAMP
		目前世界最流行的企业建站方式是LAMP（Linux+Apache+MySQL+PHP），
		
		即使用Linux作为操作系统，Apache作为Web服务器，MySQL作为数据库，PHP作为服务器端脚本解释器。
		
		这四个软件都是遵循GPL的开放源码软件，它们安全、稳定、快速、功能强大，使用它们可以建立一个快速、稳定、免费的网站系统。
		
		
		准备环境
		
		yum添加163源
		地址：
		http://mirrors.163.com/.help/centos.html
		下载方式：
		wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
		使用 yum 程序安装所需开发包（以下为标准的rpm包名称）
		
		yum install gcc gcc-c++ gcc-g77 flex bison autoconf automake bzip2-devel zlib-devel ncurses-devel 
		libjpeg-devel libpng-devel libtiff-devel freetype-devel pam-devel openssl-devel libxml2-devel 
		gettext-devel pcre-devel mysql-devel net-snmp-devel curl-devel perl-DBI
		
		安装LAMP
		
			安装mysql：
			yum -y install mysql mysql-server
		
			自启动：
			
				chkconfig --level 235 mysqld on
				
			启动服务：
				
				/etc/init.d/mysqld start
		
			配置mysql的密码：
				
				mysqladmin -u root password 123456 (自行修改!!)
				
			mysql的配置文件：
				
				/etc/my.cnf
		
		
		安装apache：
			
			yum -y install httpd
			
			自启动：
				
				chkconfig --level 235 httpd on
				
				启动服务：
					
					/etc/init.d/httpd start
			
			apache的web存放目录：
				
				/var/www/html
			
			apache的配置文件:
				
				/etc/httpd/conf/httpd.conf
				
				Listen 80
				ServerName 127.0.0.1:80
				DocumentRoot “/var/www/html”
				DirectoryIndex index.php index.html index.html.var
		
		
		安装php：
		
			yum -y install php
			
			重启httpd服务激活php：
				
				/etc/init.d/httpd restart
				
			测试php是否安装完成
			
				vim /var/www/html/info.php
				
				<?php
					phpinfo();
				?>
			
			安装php的组件：
				
				搜索php组件：
				
					yum search php
				
				安装常用组件：
					
					yum -y install php-mysql php-gd php-imap php-ldap php-odbc 
					php-pear php-xml php-xmlrpc  php-bcmath php-mbstring
				
			记得安装完要重启httpd服务激活组件:
				
				/etc/init.d/httpd restart
		
		php配置文件：
			
			vi /etc/php.ini
				
				memory_limit=128M
				post_max_size=8M
				upload_max_filesize=2M
				
				
		一条命令安装：
		
		yum -y install gcc gcc-c++ gcc-g77 flex bison autoconf automake
		bzip2-devel zlib-devel ncurses-devel libjpeg-devel libpng-devel 
		libtiff-devel freetype-devel pam-devel openssl-devel libxml2-devel 
		gettext-devel pcre-devel mysql-devel net-snmp-devel curl-devel perl-DBI 
		mysql mysql-server httpd php php-mysql php-gd php-imap php-ldap 
		php-odbc php-pear php-xml php-xmlrpc  php-bcmath php-mbstring
		
		然后配置 mysql密码和更改apache配置文件即可。
		
		
		
		
		
		
	