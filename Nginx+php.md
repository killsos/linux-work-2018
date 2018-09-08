## Nginx+php

- 编译PHP
	
					
		初始环境：
		
		为了省事把所需要的库文件全都安装上，可以使用rpm包安装，也可以用yum命令安装，
		
		yum -y install gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel 
		freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel 
		glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs 
		e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel openldap 
		openldap-devel nss_ldap openldap-clients openldap-servers 
		
		#nginx所需要的库文件件
			
		yum -y install pcre-devel zlib-devel
		
		#PHP依赖的库文件
		
		yum -y install gd-devel libjpeg-devel libpng-devel freetype-devel libxml2-devel curl-devel
		
		cp -frp /usr/lib64/libldap* /usr/lib/
		
		安装库文件
		
		tar zxvf libiconv-1.14.tar.gz  
		
		cd libiconv-1.14  
		
		./configure --prefix=/usr/local 
		
		make && make install 
		
		tar zxvf libmcrypt-2.5.8.tar.gz 
		 
		cd libmcrypt-2.5.8  
		
		./configure  
		
		make && make install
		 
		/sbin/ldconfig 
		 
		cd libltdl  
		
		./configure --enable-ltdl-install
		 
		make && make install 
		
		tar zxvf mhash-0.9.9.9.tar.gz 
		  
		cd mhash-0.9.9.9/  
		 
		./configure  
		
		make && make install 
		
		ln -s /usr/local/lib/libmcrypt.la /usr/lib/libmcrypt.la  
		ln -s /usr/local/lib/libmcrypt.so /usr/lib/libmcrypt.so  
		ln -s /usr/local/lib/libmcrypt.so.4 /usr/lib/libmcrypt.so.4  
		ln -s /usr/local/lib/libmcrypt.so.4.4.8 /usr/lib/libmcrypt.so.4.4.8  
		ln -s /usr/local/lib/libmhash.a /usr/lib/libmhash.a  
		ln -s /usr/local/lib/libmhash.la /usr/lib/libmhash.la  
		ln -s /usr/local/lib/libmhash.so /usr/lib/libmhash.so  
		ln -s /usr/local/lib/libmhash.so.2 /usr/lib/libmhash.so.2  
		ln -s /usr/local/lib/libmhash.so.2.0.1 /usr/lib/libmhash.so.2.0.1  
		ln -s /usr/local/bin/libmcrypt-config /usr/bin/libmcrypt-config 
		
		
		tar -zxvf mcrypt-2.6.8.tar.gz  
		 
		cd mcrypt-2.6.8/  
		
		/sbin/ldconfig  
		
		./configure  
		
		make && make install 
		
		mkdir -p /usr/local/php5.4  
		
		tar zxvf php-5.4.13.tar.gz  
		
		cd php-5.4.13
		
		export LIBS="-lm -ltermcap -lresolv"
		
		export DYLD_LIBRARY_PATH="/usr/local/mysql5.5/lib/:/lib/:/usr/lib/:/usr/local/lib:/lib64/:/usr/lib64/:/usr/local/lib64"
		
		export LD_LIBRARY_PATH="/usr/local/mysql5.5/lib/:/lib/:/usr/lib/:/usr/local/lib:/lib64/:/usr/lib64/:/usr/local/lib64"
		
		
		./configure --prefix=/usr/local/php5.4 \  
		--with-config-file-path=/etc \  
		--with-iconv-dir=/usr/local/ --with-freetype-dir \  
		--with-mysql=/usr/local/mysql \  
		--with-mysqli=/usr/local/mysql/bin/mysql_config \  
		--with-jpeg-dir --with-png-dir --with-zlib \  
		--with-mhash --enable-sockets --enable-ftp \  
		--with-libxml-dir --enable-xml --disable-rpath \  
		--enable-bcmath \  
		--enable-shmop --enable-sysvsem \  
		--enable-inline-optimization --with-curl \  
		--with-curlwrappers \  
		--enable-mbregex \  
		--enable-mbstring --with-mcrypt --with-gd \  
		--enable-gd-native-ttf --with-openssl --with-mhash \  
		--enable-pcntl --enable-sockets --with-ldap --with-ldap-sasl \  
		--enable-fpm \  
		--with-xmlrpc --enable-zip --enable-soap \  
		--without-pear   
		
		
		make ZEND_EXTRA_LIBS='-liconv' 
		make install 
		
		cp php.ini-production /etc/php.ini
		
		将php-fpm作为服务运行
		
		cp php-5.4.13/sapi/fpm/init.d.php-fpm  /etc/init.d/php-fpm 
		
		cd /usr/local/php5.4/etc
		
		mv php-fpm.conf.default  php-fpm.conf 
		
		chmod 755 /etc/init.d/php-fpm
		 
		chkconfig --add php-fpm 
		
		chkconfig --level 345 php-fpm on
		
		服务方式启动php-fpm
			
			service php-fpm start
					
					
					
					
					
					
					
					
					
	
- 编译Nginx

		
		useradd www
		
		安装pcre
		
			tar zxvf pcre-8.32.tar.gz
			
			cd pcre-8.32/
			
			./configure --prefix=/usr/local/pcre
			
			make && make install
			
		安装nginx
			
			tar zxvf nginx-1.0.15.tar.gz
			
			cd nginx-1.0.15/
			
			./configure --user=www --group=www 
			--prefix=/usr/local/nginx --with-http_stub_status_module 
			--with-http_ssl_module --with-http_gzip_static_module --with-ipv6
			
			make && make install
		
		
