# Phần 1: Giới thiệu về hệ thống image 
## I. Tổng quan
### 1. Giới thiệu
* Hệ thống image là hệ thống lưu trữ và xử lý về image, hệ thống sẽ xử lý về việc upload, download image. Thực hiện truy vấn tìm kiếm dữ liệu mỗi khi có request từ phía client. Bên trong hệ thống có rất nhiều thành phần để cấu thành hoàn chỉnh hệ thống. Ví dụ như api, upload, thump memcache, mongodb, mysql , mogstored.

### 2. Các thành phần 	
* Hệ thống iamge gồm các thành phần sau 
	- Image Api: bao gồm các server (6.37, 6.89)
	- Image upload: bao gồm các server (6.23, 6.48)
	- Image Thump: bao gồm các server (2.82, 10.3.16.147, 10.3.16.191, 10.3.16.192, 10.3.16.59, 10.3.16.60)
	- Image mecache: bao gồm các server (6.37, 6.89.6.23,6.48)
	- Image redis: bao gồm các server (4.18, 4.19)
	- Image mongdb: bao gồm các server (2.156, 5.27, 9.88)
	- Image mysql: bao gồm các sưeever (5.27, 9.88)
	- Image mysql-treeder: bao gồm các server (172.16.4.12, 172.16.4.11)
	- Image rqworker: bao gồm các server(4.18, 4.19)
	- Image mogilefsd:bao gồm các server (5.106, 5.105)
	- Image mogstored: bao gồm các serrver (5.106, 5.105,5.107, 5.108,4.18, 4.19)
	- Squid: Bao gồm server 192.168.2.126
	
### 3. Chức năng của từng thành phần
* Image_API: Image API đảm nhiệm chức năng trả về link ảnh cho client
* Image_upload: Đảm nhiệm việc upload image từ client lên hệ thống
* Image_Thump: Tạo thump cho image
* Image_memcached: lưu giữ một phần data của image nhằm nhiệm vụ giảm tải cho mogstored và nâng cao hiệu suất.
* Image_redis: tương tự với image_memcached cũng lưu giữ 1 phần dữ liệu để giảm tải cho mogstored
* Image_mogodb: lưu trữ database
* Image_mysql: lưu trữ database
* Image_mysqltreedir
* Image_rqworker:convert ảnh 
* Image_mogilefsd(tracker): đẩy database về mysql và mongodb
* Squid:
* mogstored: Lưu trữ data

### 4. Mô hình 
<image src="https://i.imgur.com/76DXjiU.jpg">

- Mô tả hoạt động:
	- Theo hướng download Image_Api sẽ đảm nhiệm nhiệm vụ trả về cho client đường link của image cho client. Request 
	-  Theo hướng upload, Image_uploade sẽ đảm nhiệm nhiệm vụ về việc upload image của client lên hệ thống, sau đó nó chuyển tới tracker, ở đây tracker sẽ có nhiệm vụ lấy thông tin của image và đẩy database về mongodb và mysql đồng thời đẩy ảnh về kho lưu trữ là mogstored.
