# II. Triển khai mogilefs vs mysql master-master
Mô hình sử dụng 2 máy Ubuntu 14.04 
	Máy A: 192.168.188.149
	Máy B: 192.168.188.150
	
Cài đặt và cấu hình MySQL Trên máy A:
cài đặt MySQL: 
		sudo apt-get install mysql-server mysql-client	
	
cấu hình:

Sửa file cấu hình: /etc/mysql/my.cnf
		#server-id              = 1
			#log_bin                = /var/log/mysql/mysql-bin.log
			#binlog_do_db           = include_database_name
			bind-address            = 127.0.0.1
		
		Loại bỏ comment ở 3 dòng đầu và comment dòng cuối:
			server-id               = 1
			log_bin                 = /var/log/mysql/mysql-bin.log
			binlog_do_db            = mogilefs
			# bind-address            = 127.0.0.1

khởi động lại dịch vụ mysql
		sudo service mysql restart

Tương tự với máy B
cài đặt MySQL: 
		sudo apt-get install mysql-server mysql-client	
	
cấu hình:

Sửa file cấu hình: /etc/mysql/my.cnf
		#server-id              = 1
			#log_bin                = /var/log/mysql/mysql-bin.log
			#binlog_do_db           = include_database_name
			bind-address            = 127.0.0.1
		Sửa thành: id ở đây khác với máy A 
			server-id               = 2
			log_bin                 = /var/log/mysql/mysql-bin.log
			binlog_do_db            = mogilefs
			# bind-address            = 127.0.0.1

khởi động lại dịch vụ mysql
		sudo service mysql restart

Tạo database và người dùng tương tự trên cả 2 máy:

		Truy cập vào mysql 
		mysql -u root -p
		
		mysql> CREATE DATABASE mogilefs;
		mysql> create user 'mogile'@'%' identified by 'password';
		mysql> grant replication slave on *.* to 'mogile'@'%';
		mysql> GRANT ALL ON mogilefs.* TO 'mogile'@'%';
		mysql> FLUSH PRIVILEGES;

		Trên máy A:
	        mysql> show master status;
		+------------------------+-----------+---------------------+-------------------------+
		| File                        | Position | Binlog_Do_DB | Binlog_Ignore_DB |
		+------------------------+-----------+---------------------+-------------------------+
		| mysql-bin.000002 |        107 | mogilefs           |                                |
		+------------------------+-----------+---------------------+-------------------------+
		1 row in set (0.00 sec)
		
		mysql> slave stop;
		mysql> CHANGE MASTER TO MASTER_HOST = '192.168.188.150', MASTER_USER = 'mogile', MASTER_PASSWORD = 'password', 			MASTER_LOG_FILE = 'mysql-bin.000003', MASTER_LOG_POS = 107; 
 mysql> slave start;
		
		# Lưu ý các chỉ số:    host trỏ tới địa chỉ máy B
					log file: trỏ tới file trên máy B
					log pos: là position trên máy B
		các chỉ số này có thể thay đổi.




		Trên máy B:
		mysql> show master status;
		+------------------------+-----------+---------------------+-------------------------+
		| File                        | Position | Binlog_Do_DB | Binlog_Ignore_DB |
		+------------------------+-----------+---------------------+-------------------------+
		| mysql-bin.000003 |        107 | mogilefs           |                                |
		+------------------------+-----------+---------------------+-------------------------+
		1 row in set (0.00 sec)

mysql> slave stop;
	mysql> CHANGE MASTER TO MASTER_HOST = '192.168.188.149', MASTER_USER = 'mogile', MASTER_PASSWORD = 'password', MASTER_LOG_FILE = 'mysql-bin.000002', MASTER_LOG_POS = 107; 
 mysql> slave start;
		
		# Thay các chỉ số của máy A
Cài đặt mogilefs trên máy A
Thêm repo:
		sudo apt-get install python-software-properties 
sudo add-apt-repository ppa:saz/mogilefs

Update cache và cài đặt:
sudo apt-get update 
sudo apt-get install mogilefsd mogstored mogilefs-utils perlbal

Tạo file cấu hình và đặt trong /etc/mogilefs/
sudo dpkg-reconfigure mogilefsd 
sudo dpkg-reconfigure mogstored

Sửa file cấu hình /etc/mogilefs/mogilefsd.conf
db_user = mogile
db_pass = password

Thực thi lệnh sau:
mogdbsetup --dbhost=localhost --dbname=mogilefs --dbuser=mogile --dbpassword=password

Khởi động
sudo /etc/init.d/mogilefsd start 
sudo /etc/init.d/mogstored start

Để kiểm tra ta vào mysql sau đó show tables in mogilefs, trên cả 2 máy sẽ thấy các bảng của mogilefs đã tạo ra.

