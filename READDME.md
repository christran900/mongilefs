# I. Giới thiệu
<ul>
  Mogilefs là một phân mêm mã nguồn mở của Danga. Các thuộc tính của nó bao gồm:
  * Application level: MogileFS hoạt động ở tần ứng dụng, không cần cai thêm cac modul cho hệ thống. Dễ dàng trong việc triển khai 
  * No single point of failure: 3 thành phần của MongileFS có thê được cài đặt ở nhiều máy (storage node,trackers, tracker's database), có thể chạy trackers trên cung một máy với storage node. Yêu cầu tối thiểu co 2 máy
  * automatic file replication: tuỳ thuộc vào lớp, tập tin sẽ được nhân bản ở các node, với số nhân bản tuỳ vào thiết lập cua lớp. Việc phân chia ra các lớp cho phép xác định mức độ quan trọng va mức độ sử dụng của tâp tin trong lớp đó.
  * "Better than Raid" trong thiết lập non-SAN RAID, ác đĩa cứng sẽ có đặc tính bền bỉ chứ không phải là các may tính do đêos nếu hỏng hóc xảy ra đối với máy tính thì tập tin sẽ không truy cập được. khi dùng MogileFS tập tin sẽ được nhân bản ở nhiều đĩa và nhiêu máy con khác nhau đảm bảo chúng luôn dugnf đươc ngay cả khi một sô máy tính trọng hệ thống MogileFS bi hỏng.
  * No RAID requiered: Các đĩa của từng node trong hệ thống có thể dung RAID, nhưng điêu đó không bắt buộc.
  * Local filesystem agnostic: đĩa cứng ở từng node được định dạng tuỳ theo hê thống cai ở node đó (ext3 , XFS...)MogileFS sử dụng hệ thống riêng để định vị tâp tin thư mục va do đo sẽ không găp phải các giơi hạn nhu về số tập tin tối đa trong thư mục, hoặc số thư mục con tối đa trong một thư mục 
  
 Tuy vậy mongilefs vẫn chưa được hoàn hảo.
 * Không tuân theo chuân POSIX: cac sứng dụng Unix chuân sẽ không làm việc được với MongileFS. Chỉ sử dụng Mogile để lưu trữ tâp tin va sau đo đọc ra thong tin, bằng cách thông qua cac thư viên truy cập đên hệ thống tâp tin MogileFS qua phương pháp 
 PUT/GET (HTTP Protocol)
 
* Chưa co tính linh động: Bởi vì MogileFS có một số thành phân chỉ hoạt động tron môi trường Linux
</ul>
 
