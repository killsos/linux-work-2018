## 编译安装LAMP之安装Apache+php与管理

- 编译安装Apache+PHP

				
		1.安装程序依赖库和开发环境
		
		为了省事把所需要的库文件全都安装上，可以使用rpm包安装，也可以用yum命令安装，
		
		yum -y install gcc gcc-c++ autoconf automake make libjpeg libjpeg-devel libxml2 
		libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel 
		ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel 
		libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients 
		openldap-servers libtool* mysql-devel
		
		
		ln -s /usr/lib64/mysql/libmysqlclient.so.16.0.0 /usr/lib/libmysqlclient_r.so
		
				
		2.部署apache
		
		(1)	解包 tar zxvf httpd-2.2.22.tar.gz
		
		(2)	切换目录 cd httpd-2.2.22
		
		(3)	配置 ./configure  --prefix=/usr/local/apache --enable-so --enable-proxy --enable-rewrite --with-mpm=worker
		
		(4) 编译安装：make&&make install
		
		检查模块是否安装上		
				
				
		3.Apache整合PHP
		
		(1)	安装freetype
			
			tar jxvf freetype-2.3.10.tar.bz2
			
			cd freetype-2.3.10
			
			./configure --prefix=/usr/local/freetype
			
			make
			
			make install
			
			cd ..
			
			
		(2)	安装jpeg
		
			tar zxvf jpegsrc.v7.tar.gz
			
			cd jpeg-7
			
			./configure –prefix=/usr/local/jpeg
			
			make
			
			make install
			
			cd ..
			
		(3)	安装libpng
			
			tar jxvf libpng-1.2.41.tar.bz2
			
			cd libpng-1.2.41
			
			./configure –prefix=/usr/local/libpng
			
			make
			
			make install
			
		(4)	安装GD
		
			Ｋ	链接头文件
			
			ln -s /usr/local/libpng/include/pngconf.h /usr/include
			
			ln –s /usr/local/libpng/include/png.h /usr/include
		
			Ｌ	安装GD
			
				tar zxvf gd-2.0.35.tar.gz
				
				cd gd-2.0.35
				
				./configure –prefix=/usr/local/gd2 \
				
				--with-freetype=/usr/local/freetype –with-png=/usr/local/libpng \
				
				--with-jpeg=/usr/local/jpeg
				
				make
				
				make install
				
				cd ..
				
		(5)	安装PHP
		
			tar jxvf php-5.2.17.tar.bz2
			
			cd php-5.2.17
			
			./configure --prefix=/usr/local/php \
			--with-gd=/usr/local/gd2 --with-apxs2=/usr/local/apache/bin/apxs \ 
			--enable-mbregex --enable-bcmath \
			 --with-mysql --with-zlib-dir --enable-mbstring=all \
			 --with-pdo-mysql --with-freetype-dir=/usr/local/freetype --with-jpeg-dir=/usr/local/jpeg \
			 --with-openssl \
			 --with-curl
			 
			 make
			 
			 make install
			 
			 cp php.ini-dist /usr/local/php/lib/php.ini
			 
			 cd ..
			 
			 
		(6)	修改apache配置文件
			
			cd /usr/local/apache/
			
			mkdir conf/
			
			vim httpd.conf
			
			
			在主配置文件<IfModule mime_module><\IfModule>之间插入行：
				
				AddType application/x-httpd-php .php
				
				添加apache用户，修改User和Group为：
				
				User apache
				
				Group apache
				
				ServerName localhost:80
				
				Include conf.d/*.conf
				
				
		(7)	检查
			
			
			在默认的Apache根文档目录创建文件/usr/local/apache/htdocs/test.php,其内容为：
			
			<?
			PHPinfo();
			?>
			
			
			检查是否有语法错误：/usr/local/apache/bin/apachectl –t
			
			编写启动脚本httpd放在/etc/init.d下
			
			启动apache：service httpd start
			
			输入：http://apache-ip/test.php 查看php所支持的模块加载情况
			
			
			
		4虚拟目录：
			
			vim /usr/local/apache/conf.d/vhost.conf
			
			NameVirtualHost *:80
			<VirtualHost *:80>
		    	DocumentRoot /home/linuxops/www/
		    	ServerName linuxops.com
			</VirtualHost>
		
				
				
				
				
	