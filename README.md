# Họ và tên: Lương Văn Học - MSSV:K225480106025
# Lớp:K58KTPM
# Môn: Phát triển ứng dụng với mã nguồn mở-TEE0421
# Bài tập 04: KHAI THÁC N8N ĐỂ TỰ ĐỘNG ĐĂNG BÀI LÊN WORDPRESS


# Phần 1: Sử dụng Docker trên Ubuntu để tạo file docker-compose.yml

Mục tiêu của phần này là xây dựng một môi trường chạy WordPress trên Ubuntu bằng Docker Compose, gồm các dịch vụ:

- MariaDB
- phpMyAdmin
- WordPress
- Cloudflared
- N8n

### B1:Chuyển vào thư mục dự án ```wordpress-project```

```
cd ~/wordpress-project
```
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/282b62dc-bfd2-41b2-a279-df1c495ffdd7" />

### B2: Mở file ```docker-compose.yml``` bằng trình soạn thảo nano để chỉnh sửa nội dung

```
nano docker-compose.yml
```
**Nội dung file docker-compose.yml**

```
services:
  db:
    image: mariadb:latest
    container_name: wordpress_db
    restart: unless-stopped
    environment:
      TZ: Asia/Ho_Chi_Minh
      MARIADB_ROOT_PASSWORD: rootpassword
      MARIADB_DATABASE: wordpress
      MARIADB_USER: wpuser
      MARIADB_PASSWORD: wppassword
    volumes:
      - db_data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin:latest
    container_name: wordpress_phpmyadmin
    restart: unless-stopped
    depends_on:
      - db
    environment:
      PMA_HOST: db
      PMA_PORT: 3306
      PMA_ARBITRARY: 1
    ports:
      - "8080:80"

  wordpress:
    image: wordpress:latest
    container_name: wordpress_site
    restart: unless-stopped
    depends_on:
      - db
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppassword
    volumes:
      - wp_data:/var/www/html
    ports:
      - "8081:80"

  cloudflared:
    image: cloudflare/cloudflared:latest
    container_name: wordpress_cloudflared
    restart: unless-stopped
    command: tunnel --no-autoupdate run --token eyJhIjoiZWNhNDdhZWZiNmMyZWM3MWVkMWY5ZmI3NDZjZmQwNzMiLCJ0IjoiNTg4ZGZhNDQtN2YyOS00YjI4LWI5N2QtNGQ1MzRkOTU0NDhmIiwicyI6Ik1UTXdNemcyTWpFdFl6ZGpZaTAwWVRreUxXSXhZVFl0TWpSa01qYzVPVGt5T0RReE5qQXhaRFV6TjJRdFlqUTFOaTAwWmpKa0xUazROV010WXpnNU5UZzFPRFV4TkdReSJ9
    dns:
      - 1.1.1.1
      - 8.8.8.8
    depends_on:
      - wordpress
      - phpmyadmin

  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    environment:
      N8N_HOST: n8n.luongvanhoc.io.vn
      N8N_PROTOCOL: https
      WEBHOOK_URL: https://n8n.luongvanhoc.io.vn/
      N8N_SECURE_COOKIE: false
    ports:
      - "5678:5678"
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  db_data:
  wp_data:
  n8n_data:
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d595ac45-6860-4087-b814-68262f0197f3" />

### B4: Thêm public hostname

- Mở tab Routes Trong tunnel wordpress-tunnel

- Chọn tab Routes -> Bấm Add route

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/2f927f3e-cf1e-468e-975a-d2e0fc0d9bf1" />

- Chọn: Published application

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/1d5a14dd-c325-4636-9c2c-7a582b857601" />

- **Điền thông tin route Tại cửa sổ Add published application, điền:**

Tạo **pma.luongvanhoc.io.vn**
  
+ Subdomain: pma
+ Domain: luongvanhoc.io.vn
+ Service URL: http://phpmyadmin:80

<img width="1918" height="1030" alt="image" src="https://github.com/user-attachments/assets/cd138d73-b9c4-475f-8627-10386d74567e" />

Tạo **n8n.luongvanhoc.io.vn**

+ Subdomain: n8n
+ Domain: luongvanhoc.io.vn
+ Service URL: http://n8n:5678

<img width="1924" height="1038" alt="image" src="https://github.com/user-attachments/assets/e8813a92-c740-4ffc-9bbf-d19b06c84af0" />

**Các route khi tạo xong:**

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/45593833-cb0f-479b-9d1d-13bdf675d760" />

### Kiểm tra

**Truy cập phpMyAdmin để kiểm tra cơ sở dữ liệu**
  
- Truy cập:```https://pma.luongvanhoc.io.vn```

- Kết quả mong đợi
  
•	CSDL tồn tại

•	Chưa có bảng dữ liệu nào

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6dc4cff9-4c63-4a88-ac56-a5e0699d3713" />

**Truy cập WordPress để cài đặt**

- Truy cập:```https://blog.luongvanhoc.io.vn```

Sau đó làm theo trình hướng dẫn cài đặt của WordPress:

•	chọn ngôn ngữ

•	đặt tên website

•	tạo tài khoản quản trị

•	đặt mật khẩu

•	hoàn tất cài đặt

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a7f7f762-2a3f-442e-89c6-85efefb7f415" />

Kết quả: WordPress được khởi tạo thành công và có thể đăng nhập vào trang quản trị.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/59c2370c-7196-4857-9677-ac176cc66fb3" />

**Kiểm tra lại cơ sở dữ liệu sau khi cài WordPress**

Quay lại:```https://pma.luongvanhoc.io.vn```

Kết quả: CSDL đã có bảng dữ liệu, chứng tỏ WordPress đã kết nối thành công với MariaDB.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/707d350f-1253-4eeb-8367-7bb7b54e8bcb" />

**Tạo các bài viết trên WordPress**

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f88da0e2-0541-4172-813f-ff29d2ad13ba" />

Bài viết 2: Tạo một bài viết giới thiệu về những kiến thức đã học được trong môn Phát triển ứng dụng với mã nguồn mở.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/70bbc547-5f5a-4924-b94a-3abba6399665" />

**Chuẩn bị n8n để tự động đăng bài lên WordPress**

Truy cập:https://n8n.luongvanhoc.io.vn

Thực hiện các bước:

•	tạo tài khoản admin

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1ac87d76-5bab-4400-b5c9-65bab6caf1a2" />

•	Điền khảo sát ban đầu của n8n
Hệ thống chuyển sang màn hình Customize n8n to you.
Tôi chọn các mục phù hợp với mục đích học tập cá nhân:

What best describes your company? → chọn Personal use
Which role best describes you? → chọn Student
Who will your automations mainly be for? → chọn Myself
How big is your company? → chọn Just me hoặc mục nhỏ nhất
How did you hear about n8n? → chọn mục gần nhất như Search engine
Sau đó bấm Get started để vào giao diện chính của n8n.

Bước 3: Tạo workflow mới
Tại trang chủ n8n, tôi chọn:

Text
Start from scratch
để tạo một workflow trống mới.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0548087d-9dc8-4557-bd95-4eca84be8ab9" />
Bước 4: Thêm node Telegram Trigger
Trong workflow mới, tôi bấm vào Add first step và tìm node:

Text
Telegram Trigger
Sau đó chọn trigger:

Text
On message
vì workflow cần chạy khi có tin nhắn mới gửi vào bot Telegram.


Bước 5: Cấu hình Telegram credential
Trong node Telegram Trigger, tôi bấm:

Text
Set up credential
rồi nhập Bot Token lấy từ @BotFather trên Telegram.

Sau khi lưu credential, tôi mở bot Telegram của mình và nhắn một tin bất kỳ, ví dụ:

Text
hello
Đồng thời bấm Execute step hoặc Test this trigger trong n8n.

Kết quả trả về dữ liệu JSON của tin nhắn Telegram, chứng tỏ node Telegram Trigger hoạt động thành công.

Bước 6: Kiểm tra kết quả
Sau khi node nhận được dữ liệu, bên phải màn hình xuất hiện OUTPUT và thông báo:

Text
Node executed successfully
Điều này xác nhận rằng n8n đã kết nối thành công với bot Telegram và sẵn sàng cho các bước xử lý tiếp theo.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fb25a3f1-513a-4e37-a830-f6750cd895fe" />


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b523c9ed-a035-421e-a190-efa7d94e9b46" />


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5d6ba2de-04c7-4e22-a920-3249d12e96d5" />


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/994cb786-c101-4d90-8a8a-d224eb71d905" />












# Phân2: Yêu cầu: sau khi có 5 service này trong file docker-compose.yml :

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1d841d9f-839d-4f96-a050-ac4954c5e18a" />






