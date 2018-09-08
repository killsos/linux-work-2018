##什么是Mysql

	MyQL是一个开放源码的小型关系型数据库管理系统，开发者为瑞典MySQL  
	
	AB公司。目前MySQL被广泛地应用在Internet上的中小型网站中。  
	
	由于其体积小、速度快、总体拥有成本低，尤其是开放源码这一特点，  
	
	许多中小型网站为了降低网站总体拥有成本而选择了MySQL作为网站数据库。  
	
	


## 安装Mysql

- 1 安装简介
		
		用户名：mysql
		
		安装目录：/usr/local/mysql-5.5
		
		数据库目录：/data/mysql/data
		
		源码包：mysql-5.5.28.tar.gz
		

- 2 安装准备
		
		添加用户
		
		useradd -s /sbin/nologin mysql
		
		建立所需目录
		
		mkdir -p /data/mysql/data
		
		chown -R mysql:mysql /data/mysql
		
		安装编译所需文件或程序
		
		yum install gcc gcc-c++ cmake ncurses-devel bison
		
		下载源码包
		
			http://sourceforge.net/projects/mysql.mirror/files/MySQL%205.5.28/
			
			wget http://ncu.dl.sourceforge.net/project/mysql.mirror/MySQL%205.5.28/mysql-5.5.28.tar.gz

- 3 编译安装
	
		cd 
		tar xzvf mysql-5.5.28.tar.gz
		cd mysql-5.5.28
		
		cmake -DMYSQL_USER=mysql -DCMAKE_INSTALL_PREFIX=/usr/local/mysql5.5 -DINSTALL_DATADIR=/data/mysql/data 
		-DMYSQL_UNIX_ADDR=/tmp/mysqld.sock -DDEFAULT_CHARSET=utf8  -DDEFAULT_COLLATION=utf8_general_ci 
		-DEXTRA_CHARSETS=all -DWITH_EMBEDDED_SERVER=1 -DENABLED_LOCAL_INFILE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 
		-DWITH_INNOBASE_STORAGE_ENGINE=1
		
		参数说明:
			
			-DCMAKE_INSTALL_PREFIX=/usr/local/mysql //安装目录
			
			-DMYSQL_DATADIR=/usr/local/mysql/data //数据库存放目录
			
			-DWITH_MYISAM_STORAGE_ENGINE=1 //安装myisam存储引擎
			
			-DWITH_INNOBASE_STORAGE_ENGINE=1 //安装innodb存储引擎
			
			-DWITH_ARCHIVE_STORAGE_ENGINE=1 //安装archive存储引擎
			
			-DWITH_BLACKHOLE_STORAGE_ENGINE=1 //安装blackhole存储引擎
			
			-DENABLED_LOCAL_INFILE=1 //允许从本地导入数据
			
			-DDEFAULT_CHARSET=utf8 　　//使用utf8字符
			
			-DDEFAULT_COLLATION=utf8_general_ci //校验字符
			
			-DEXTRA_CHARSETS=all 　　//安装所有扩展字符集
			
			-DMYSQL_TCP_PORT=3306 //MySQL监听端口
			
			-DMYSQL_USER=mysql //MySQL用户名
			
		其他参数:
			
			-DWITH-EMBEDDED_SERVER=1 //编译成embedded MySQL library (libmysqld.a)
			
			-DSYSCONFDIR=/etc //MySQL配辑文件
			
			-DMYSQL_UNIX_ADDR=/tmp/mysqld.sock //Unix socket 文件路径
			
			-DWITH_READLINE=1 //快捷键功能
			
			-DWITH_SSL=yes //SSL
			
			-DWITH_MEMORY_STORAGE_ENGINE=1 //安装memory存储引擎
			
			-DWITH_FEDERATED_STORAGE_ENGINE=1 //安装frderated存储引擎
			
			-DWITH_PARTITION_STORAGE_ENGINE=1 //安装数据库分区
			
			-DINSTALL_PLUGINDIR=/usr/local/mysql/plugin //插件文件及配置路径
			
		
		make && make install
		
		chown -R mysql:mysql /usr/local/mysql5.5/
		
		mv /root/mysql-5.5.28/support-files/my-large.cnf /etc/my.cnf
		
		mv /root/mysql-5.5.28/support-files/mysql.server /etc/init.d/mysqld
		
		chmod a+x /etc/init.d/mysqld

- 开启启动
	
		
		chkconfig --level 345 mysqld on
		
		设置环境变量
		
		echo "export PATH=/usr/local/mysql5.5/bin/:$PATH" >> /etc/profile
		
		source /etc/profile
		
		/usr/local/mysql5.5/scripts/mysql_install_db --user=mysql --defaults-file=/etc/my.cnf 
		
		--basedir=/usr/local/mysql5.5 --datadir=/data/mysql/data
		
		vim /etc/my.cnf
		
		basedir=/usr/local/mysql5.5
		datadir=/data/mysql/data
		

- 创建mysql管理员
	
		
		/usr/bin/mysqladmin -u root password 'z'
		
		连接数据库：
		
		mysql -u root -p  -P port
	
	
