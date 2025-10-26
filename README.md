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
```
iisreset /stop
```
2.2. Tải Apache

Tải từ: https://www.apachelounge.com/download/

File ví dụ: httpd-2.4.65-250724-win64-VS17.zip

2.3. Giải nén

Giải nén vào:
D:\Apache24\

2.4. Sửa file cấu hình

Mở D:\Apache24\conf\httpd.conf
```
Define SRVROOT "D:/Apache24"
ServerName localhost:80
Include conf/extra/httpd-vhosts.conf
```
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
```
127.0.0.1 lamnguyenminhluong.com
```
2.7. Cài đặt Apache làm service
```
cd D:\Apache24\bin
httpd.exe -k install
httpd.exe -k start
```
→ Mở trình duyệt: http://lamnguyenminhluong.com

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/ad5f1978-8961-414d-b828-98d56612cb56" />


 3. Cài đặt Node.js và Node-RED
3.1. Tải Node.js

Vào https://nodejs.org/en

Tải bản LTS: node-v20.19.5-x64.msi

Cài đặt tại: D:\nodejs

3.2. Cài Node-RED

Mở CMD (Administrator):
```
npm install -g --unsafe-perm node-red
```

Kiểm tra:

node-red

Truy cập: http://localhost:1880


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/968ddc44-9506-42f6-b9c8-a94b46227e09" />


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/ee786ace-74f8-4204-bf8b-5114dc2af7bd" />


3.3. Cài Node-RED làm service (tùy chọn)

Tải nssm từ https://nssm.cc/download

Mở CMD:
```
nssm install NodeRED
```
Điền đường dẫn Node.js + đối số node-red

Start service.

 4. Tạo CSDL SQL Server

Mở SQL Server Management Studio

Tạo database: BT2

Tạo bảng ví dụ:
```
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
```

Nhập dữ liệu mẫu

 5. Kết nối Node-RED với SQL Server

Mở Node-RED: http://localhost:1880

Cài node:
```
node-red-contrib-mssql-plus

node-red-node-mysql

node-red-contrib-telegrambot

node-red-contrib-moment

node-red-contrib-cron-plus
```
Kéo node http in → function → mssql → http response

Cấu hình mssql:
```
{
  "user": "sa",
  "password": "123",
  "server": "127.0.0.1,1433",
  "database": "BT2",
  "port": 1433
}
```

Trong node function:
```
var ten = msg.req.query.q || '';
msg.topic = `SELECT * FROM ThietBi WHERE TenThietBi LIKE N'%${ten}%'`;
return msg;
```

Đặt http in URL = /timkiem

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
```
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tìm kiếm Thiết Bị - Lang Nguyễn Minh Lương</title>
    <link rel="stylesheet" href="langnguyenminhluong.css">
</head>
<body>
    <div class="container">
        <h1>🔍 Tra cứu thiết bị thông minh</h1>
        <p class="intro">Hệ thống tìm kiếm thiết bị trong CSDL Node-RED - MSSQL</p>

        <form id="searchForm">
            <input type="text" id="keyword" placeholder="Nhập tên thiết bị (VD: cảm biến...)" required>
            <button type="submit">Tìm kiếm</button>
        </form>

        <div id="result" class="result"></div>
    </div>

    <footer>
        <p>© 2025 - Thiết kế bởi <b>Lang Nguyễn Minh Lương</b></p>
    </footer>

    <script src="langnguyenminhluong.js"></script>
</body>
</html>

```
6.2. langnguyenminhluong.css
```
body {
    font-family: "Segoe UI", sans-serif;
    background: linear-gradient(135deg, #0099ff, #00cc99);
    margin: 0;
    padding: 0;
    color: #fff;
}

.container {
    width: 80%;
    margin: 80px auto;
    text-align: center;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 16px;
    padding: 40px;
    box-shadow: 0 0 20px rgba(0,0,0,0.3);
}

h1 {
    font-size: 2em;
    margin-bottom: 10px;
}

.intro {
    font-size: 1.1em;
    color: #e0f7fa;
    margin-bottom: 30px;
}

form {
    margin-bottom: 30px;
}

input[type="text"] {
    width: 60%;
    padding: 10px;
    font-size: 16px;
    border: none;
    border-radius: 8px;
    outline: none;
}

button {
    padding: 10px 20px;
    background-color: #0044cc;
    color: white;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-weight: bold;
}

button:hover {
    background-color: #002f8a;
}

.result {
    margin-top: 20px;
    text-align: left;
    background-color: rgba(255,255,255,0.2);
    padding: 20px;
    border-radius: 10px;
}

.device {
    background-color: rgba(0,0,0,0.3);
    border-left: 5px solid #00e6ac;
    margin-bottom: 10px;
    padding: 15px;
    border-radius: 8px;
}

.device h3 {
    margin: 0;
    color: #00ffff;
}

footer {
    text-align: center;
    padding: 20px;
    font-size: 0.9em;
    background: rgba(0,0,0,0.2);
}

```
6.3. langnguyenminhluong.js
```
document.getElementById("searchForm").addEventListener("submit", async function(e) {
    e.preventDefault();

    const keyword = document.getElementById("keyword").value.trim();
    const resultDiv = document.getElementById("result");
    resultDiv.innerHTML = "<p>⏳ Đang tìm kiếm, vui lòng đợi...</p>";

    try {
        // Gọi API Node-RED bạn đã tạo ở bước 2.5
        const response = await fetch(`http://localhost:1880/timkiem?q=${encodeURIComponent(keyword)}`);
        const data = await response.json();

        if (data.length === 0) {
            resultDiv.innerHTML = "<p>⚠️ Không tìm thấy thiết bị nào phù hợp!</p>";
            return;
        }

        // Hiển thị kết quả
        let html = `<h2>Kết quả tìm kiếm (${data.length} thiết bị):</h2>`;
        data.forEach((item, i) => {
            html += `
                <div class="device">
                    <h3>${i + 1}. ${item.TenThietBi}</h3>
                    <p><b>Trạng thái:</b> ${item.TrangThai}</p>
                    <p><b>Giá trị:</b> ${item.GiaTri}</p>
                    <p><b>Thời gian:</b> ${new Date(item.ThoiGian).toLocaleString()}</p>
                </div>
            `;
        });

        resultDiv.innerHTML = html;
    } catch (error) {
        console.error("Lỗi khi truy vấn:", error);
        resultDiv.innerHTML = "<p style='color: red;'>❌ Không thể kết nối tới máy chủ Node-RED!</p>";
    }
});

```

→ Mở trình duyệt: http://localhost/langnguyenminhluong/index.html

Nếu hiện dữ liệu từ SQL ==> Hoàn tất 

 7. Viết phần nhận xét cá nhân
    Qua bài tập này, em đã hiểu rõ quy trình cài đặt và cấu hình các phần mềm như Apache, Node.js, Node-RED và SQL Server, cũng như cách thêm các thư viện cần thiết để hệ thống hoạt động. Em nắm được cách sử dụng Node-RED để tạo API back-end, xử lý dữ liệu và kết nối với cơ sở dữ liệu SQL Server. Đồng thời, em hiểu cách front-end (HTML/CSS/JS) giao tiếp với back-end thông qua API, gửi yêu cầu tìm kiếm và nhận dữ liệu JSON để hiển thị lên giao diện. Bài tập giúp em củng cố kiến thức về mô hình client–server và quy trình xây dựng một ứng dụng web hoàn chỉnh.
