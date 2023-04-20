## Các bước thực hiện master-slave
### 1. Thông tin và cài đặt
- Ip master: 10.0.9.101
- Ip slave: 10.0.9.102
#### Cài mysql server trên cả 2 máy
`sudo apt update`

`sudo apt upgrade`

`sudo apt install mysql-server`

Sau khi cài đặt xong mysql thì chúng ta tiến hành đặt password cho root
` sudo mysql_secure_installation `
Sau đó chọn y để tiếp tục 
Có 3 level đặt mật khẩu từ 0 là nhỏ nhất cho đến 2 là mạnh nhất, ở đây để cho đơn giản thì chọn 0
Sau đó nhập mật khẩu mình cần đặt rồi y liên tục là xong.
#### Trong trường hợp đặt mật khẩu bị lỗi thì:
- Thoát khỏi mysql_secure_installation bằng cách dùng phím ctrl + z
- Sau đấy ta dùng ` sudo mysql -u root -p` ( dòng mật khẩu ấn enter là được)
- Sau đấy ta nhập theo thứ tự:
`use mysql;

update user set plugin='mysql_native_password' where user='root';

flush privileges;

exit;`
