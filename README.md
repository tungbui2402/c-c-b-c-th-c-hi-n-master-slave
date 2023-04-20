# Các bước thực hiện master-slave
## 1. Thông tin và cài đặt
- Ip master: 10.0.9.101
- Ip slave: 10.0.9.102
### Cài mysql server trên cả 2 máy
```
sudo apt update
sudo apt upgrade
sudo apt install mysql-server
```

Sau khi cài đặt xong mysql thì chúng ta tiến hành đặt password cho root
`sudo mysql_secure_installation`
Sau đó chọn y để tiếp tục 
Có 3 level đặt mật khẩu từ 0 là nhỏ nhất cho đến 2 là mạnh nhất, ở đây để cho đơn giản thì chọn 0
Sau đó nhập mật khẩu mình cần đặt rồi y liên tục là xong.
#### Trong trường hợp đặt mật khẩu bị lỗi thì:
- Thoát khỏi mysql_secure_installation bằng cách dùng phím ctrl + z
- Sau đấy ta dùng ` sudo mysql -u root -p` ( dòng mật khẩu ấn enter là được)
- Sau đấy ta nhập theo thứ tự:
 ```
use mysql;
update user set plugin='mysql_native_password' where user='root';
flush privileges;
exit;
```
Sau đấy thì chúng ta quay lại mysql_secure_installation rồi nhập mật khẩu cần đặt là xong.
## 2. Thiết lập master-slave
### 2.1. Thiết lập master
- Đầu tiên chúng ta chạy lệnh `sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf` để thiết lập cấu hình mysql
- Sau đó ta tìm đến dòng `bind-address=127.0.0.1` rồi thay đổi thông số về 0.0.0.0
- Sau đó ta tìm tiếp 2 dòng là `server-id=1` và `log_bin=/var/log/mysql/mysql-bin.log` và bỏ # ở trước đi
- Sau khi thiết lập xong thì chúng ta lưu lại rồi sử dụng lệnh `sudo systemctl restart mysql` để khởi động lại mysql
- Tiếp theo vào mysql bằng `myslq -u root -p`
- Tạo 1 tài khoản truy cập:
`create user 'tung1'@'%' identified by 'Tung_2402';`
- Cấp quyền cho tài khoản vừa tạo:
```
Grant replication slave on *.* to 'tung1'@'%';
Grant all privileges on *.* to 'tung1'@'%';
Flush privileges;
```
- Sau khi cấp quyền xong thì chạy lệnh `show master status\G` để lấy thông tin file và pos:
```
************************** 1. row ***************************
 File: mysql-bin.000009           
 Position: 1386       
 Binlog_Do_DB:
 Binlog_Ignore_DB:
 Executed_Gtid_Set:
 1 row in set (0.01 sec)
```
### 2.2. Thiết lập slave
- Đầu tiên chúng ta chạy lệnh `sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf` để thiết lập cấu hình mysql
- Sau đó ta tìm đến dòng `bind-address=127.0.0.1` rồi thay đổi thông số về 0.0.0.0
- Sau đó ta tìm tiếp 2 dòng là `server-id=2 và `log_bin=/var/log/mysql/mysql-bin.log` và bỏ # ở trước đi
- Sau khi thiết lập xong thì chúng ta lưu lại rồi sử dụng lệnh `sudo systemctl restart mysql` để khởi động lại mysql
- Tiếp theo vào mysql bằng `myslq -u root -p`
- Dùng lệnh `stop slave` để dừng slave
- Sau đó cấu hình các câu lệnh:
```
CHANGE MASTER TO
	MASTER_HOST='10.0.9.101',
	MASTER_USER='tung1',
	MASTER_PASSWORD='Tung_2402',
	MASTER_LOG_FILE='mysql-bin.000009',
	MASTER_LOG_POS=1386;
```
- Sau đó chúng ta `start slave` là xong.
- Kiểm tra trạng thái slave bằng lệnh `show slave status\G`
- Chú ý 3 dòng:
```
Slave_IO_State: Waiting for source to send event
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```
Nếu hiện 3 dòng như trên thì là thành công.
