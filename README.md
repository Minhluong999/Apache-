# BÀI TẬP 02 – LẬP TRÌNH WEB

Sinh viên: Lăng Nguyễn Minh Lượng
MSSV: K225480106044


## I. MỤC TIÊU

Xây dựng một website cá nhân có front-end (HTML/CSS/JS) chạy trên Apache Web Server, kết nối tới back-end Node-RED thông qua API, và sử dụng CSDL SQL Server 2022 để lưu trữ dữ liệu.
Toàn bộ tiến trình được ghi lại trên GitHub.

## II. HƯỚNG DẪN TỪNG BƯỚC
 1. Tạo repository GitHub

Truy cập: https://github.com

Tạo repo tên: Apache-

Chọn Public.

Tạo file README.md để ghi lại quá trình làm việc, chụp màn hình, ghi chú chi tiết các bước.

Cập nhật tiến trình 15–30 phút/lần.

 2. Cài đặt Apache Web Server
 2.1. Tắt IIS (nếu có)

Mở CMD (adm)

nhập lệnh

iisreset /stop

2.2. Tải Apache

Tải từ: https://www.apachelounge.com/download/

File ví dụ: httpd-2.4.65-250724-win64-VS17.zip

2.3. Giải nén

Giải nén vào:
D:\Apache24\

2.4. Sửa file cấu hình

Mở D:\Apache24\conf\httpd.conf

Define SRVROOT "D:/Apache24"

ServerName localhost:80

Include conf/extra/httpd-vhosts.conf

2.5. Tạo Virtual Host

Mở D:\Apache24\conf\extra\httpd-vhosts.conf, thêm:
```
<VirtualHost *:80>
    ServerAdmin admin@lamnguyenminhluong.com
    DocumentRoot "D:/Apache24/lamnguyenminhluong"
    ServerName lamnguyenminhluong.com
    ErrorLog "logs/lam_error.log"
    CustomLog "logs/lam_access.log" common
</VirtualHost>
```
2.6. Map domain

Mở file: C:\Windows\System32\drivers\etc\hosts

127.0.0.1 lamnguyenminhluong.com

2.7. Cài đặt Apache làm service
httpd.exe -k install
httpd.exe -k start


→ Mở trình duyệt: http://lamnguyenminhluong.com

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/ad5f1978-8961-414d-b828-98d56612cb56" />


 3. Cài đặt Node.js và Node-RED
3.1. Tải Node.js

Vào https://nodejs.org/en

Tải bản LTS: node-v20.19.5-x64.msi

Cài đặt tại: E:\DataAutoBackup\nodejs

3.2. Cài Node-RED

Mở CMD (Administrator):

npm install -g --unsafe-perm node-red


Kiểm tra:

node-red


Truy cập: http://localhost:1880


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/968ddc44-9506-42f6-b9c8-a94b46227e09" />


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/ee786ace-74f8-4204-bf8b-5114dc2af7bd" />


3.3. Cài Node-RED làm service (tùy chọn)

Tải nssm từ https://nssm.cc/download

Mở CMD:

nssm install NodeRED


Điền đường dẫn Node.js + đối số node-red

Start service.

 4. Tạo CSDL SQL Server

Mở SQL Server Management Studio

Tạo database: BT2

Tạo bảng ví dụ:

USE BT2;
GO
CREATE TABLE dbo.ThietBi (
    ID INT IDENTITY(1,1) PRIMARY KEY,
    TenThietBi NVARCHAR(100),
    TrangThai NVARCHAR(50),
    GiaTri FLOAT,
    ThoiGian DATETIME DEFAULT GETDATE()
);
GO


Nhập dữ liệu mẫu

 5. Kết nối Node-RED với SQL Server

Mở Node-RED: http://localhost:1880

Cài node:

node-red-contrib-mssql-plus

node-red-node-mysql

node-red-contrib-telegrambot

node-red-contrib-moment

node-red-contrib-cron-plus

Kéo node http in → function → mssql → http response

Cấu hình mssql:

{
  "user": "sa",
  "password": "123",
  "server": "localhost",
  "database": "My_Profile",
  "port": 1433
}


Trong node function:

msg.payload = "SELECT * FROM ThongTinCaNhan";
return msg;


Đặt http in URL = /thongtin

Deploy → Test:

http://localhost:1880/timkiem?q=cảm


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/61854e6c-d5e8-4a31-a512-8d1f69c44356" />


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/2c8955e0-3b2f-4401-a37a-2be5bd224e2b" />

6. Tạo Front-End HTML/JS/CSS

Tạo thư mục:

D:\Apache24\htdocs\langnguyenminhluong\

chạy http://localhost/langnguyenminhluong/

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/b14393e8-4041-4045-a6d6-2e9c53059a6e" />

6.1. index.html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Thông tin cá nhân</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Hồ sơ cá nhân – Lâm Nguyễn Minh Lượng</h1>
  <div id="info"></div>
  <script src="main.js"></script>
</body>
</html>

6.2. style.css
body {
  font-family: Arial;
  background: #f4f4f4;
  padding: 40px;
}
#info {
  background: white;
  padding: 20px;
  border-radius: 10px;
}

6.3. main.js
fetch('http://localhost:1880/thongtin')
  .then(res => res.json())
  .then(data => {
    let html = '';
    data.forEach(row => {
      html += `<p><b>${row.HoTen}</b> - ${row.NgaySinh} - ${row.GioiTinh} - ${row.SoThich}</p>`;
    });
    document.getElementById('info').innerHTML = html;
  })
  .catch(err => console.error(err));


→ Mở trình duyệt: http://lamnguyenminhluong.com

Nếu hiện dữ liệu từ SQL ==> Hoàn tất 

 7. Viết phần nhận xét cá nhân
