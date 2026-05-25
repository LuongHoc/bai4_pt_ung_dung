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

## B1:Chuyển vào thư mục dự án ```wordpress-project```

```
cd ~/wordpress-project
```
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/282b62dc-bfd2-41b2-a279-df1c495ffdd7" />

## B2: Mở file ```docker-compose.yml``` bằng trình soạn thảo nano để chỉnh sửa nội dung

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

# Phân 2:

## 1. Thêm public hostname

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


## 2. Truy cập phpMyAdmin để kiểm tra cơ sở dữ liệu
  
- Truy cập:```https://pma.luongvanhoc.io.vn```

- Kết quả mong đợi
  
•	CSDL tồn tại

•	Chưa có bảng dữ liệu nào

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6dc4cff9-4c63-4a88-ac56-a5e0699d3713" />

## 3. Truy cập WordPress để cài đặt

**B1: Truy cập:```https://blog.luongvanhoc.io.vn```**

**B2: Sau đó làm theo trình hướng dẫn cài đặt của WordPress:**

•	chọn ngôn ngữ

•	đặt tên website

•	tạo tài khoản quản trị

•	đặt mật khẩu

•	hoàn tất cài đặt

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a7f7f762-2a3f-442e-89c6-85efefb7f415" />

Kết quả: WordPress được khởi tạo thành công và có thể đăng nhập vào trang quản trị.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/59c2370c-7196-4857-9677-ac176cc66fb3" />

**B3: Kiểm tra lại cơ sở dữ liệu sau khi cài WordPress**

Quay lại:```https://pma.luongvanhoc.io.vn```

Kết quả: CSDL đã có bảng dữ liệu, chứng tỏ WordPress đã kết nối thành công với MariaDB.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/707d350f-1253-4eeb-8367-7bb7b54e8bcb" />

**B4: Tạo các bài viết trên WordPress**

Bài viết 1: Giới thiệu về bản thân tôi

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f88da0e2-0541-4172-813f-ff29d2ad13ba" />

Bài viết 2: Tạo một bài viết giới thiệu về những kiến thức đã học được trong môn Phát triển ứng dụng với mã nguồn mở.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/70bbc547-5f5a-4924-b94a-3abba6399665" />

## 4. Chuẩn bị n8n để tự động đăng bài lên WordPress

**Truy cập:```https://n8n.luongvanhoc.io.vn```**

B1: Tạo tài khoản admin

- Email: luonghoc2604@gmail.com

- First Name: Học

- Last Name: Lương

- Password: Hoc@02062004

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1ac87d76-5bab-4400-b5c9-65bab6caf1a2" />

B2: Điền khảo sát ban đầu của n8n

Hệ thống chuyển sang màn hình Customize n8n to you. Chọn các mục phù hợp với mục đích học tập cá nhân:

- What best describes your company? → chọn Personal use
- Which role best describes you? → chọn Student
- Who will your automations mainly be for? → chọn Myself
- How big is your company? → chọn Just me hoặc mục nhỏ nhất
- How did you hear about n8n? → chọn mục gần nhất như Search engine
  
Sau đó bấm Get started để vào giao diện chính của n8n.

Bước 3: Tạo workflow mới
Tại trang chủ n8n, tôi chọn:```Start from scratch``` để tạo một workflow trống mới.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0548087d-9dc8-4557-bd95-4eca84be8ab9" />

Bước 4: Thêm node Telegram Trigger
Trong workflow mới, tôi bấm vào ```Add first step``` và tìm node:

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b523c9ed-a035-421e-a190-efa7d94e9b46" />

- bấm tìm kiếm```Telegram Trigger```
  
- Sau đó chọn trigger:```On message``` vì workflow cần chạy khi có tin nhắn mới gửi vào bot Telegram.

Bước 5: Cấu hình Telegram credential
Trong node Telegram Trigger, tôi bấm:```Set up credential```



<img width="1904" height="1040" alt="image" src="https://github.com/user-attachments/assets/f1050fb5-644c-498a-b80d-da8f47a8c749" />

rồi nhập Bot Token lấy từ @BotFather trên Telegram.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5d6ba2de-04c7-4e22-a920-3249d12e96d5" />

Sau khi lưu credential, tôi mở bot Telegram của mình và nhắn một tin bất kỳ, ví dụ:
```
hello
```
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9c806dba-33e1-47b5-957d-240ad4051b53" />

Đồng thời bấm Execute step trong n8n.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/994cb786-c101-4d90-8a8a-d224eb71d905" />

- Kết quả trả về dữ liệu JSON của tin nhắn Telegram, chứng tỏ node Telegram Trigger hoạt động thành công.
- Sau khi node nhận được dữ liệu, bên phải màn hình xuất hiện OUTPUT và thông báo:```Node executed successfully```
- Điều này xác nhận rằng n8n đã kết nối thành công với bot Telegram và sẵn sàng cho các bước xử lý tiếp theo.

## 5.Tạo luồng xử lý dữ liệu

Workflow của bạn gồm 4 node chính:

Telegram Trigger → Message a model → Code in JavaScript → WordPress Create a Post

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4faa9827-8d08-4756-8387-1acb948e6c1f" />

**5.1. Cấu hình node Message a model**
Node này dùng để gửi nội dung từ Telegram sang Gemini.

Bước 1: Thêm node 

- Bấm dấu + sau Telegram Trigger
- Chọn node Message a model

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/4da1bdac-9e32-47e5-a84b-355093d88f8d" />

Bước 2: Chọn credential Gemini

Trong phần credential:

- Chọn hoặc tạo credential Gemini
- Điền API key Gemini
- Lưu lại

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fffec3bb-e5e3-4869-8ff5-05d2208168ef" />

Bước 3: Chọn model

EM đã dùng model:```models/gemini-2.5-flash```

Bước 4: Nhập prompt
Trong ô Prompt, nhập:

```
{{ $json.message.text }}. Hãy trả về CHỈ một JSON hợp lệ với 2 trường: post_title và post_content. Không được thêm giải thích, không được bọc trong markdown code block. post_content phải là HTML hoàn chỉnh dùng cho WordPress.
```
Ý nghĩa:
- lấy nội dung từ Telegram
- yêu cầu Gemini trả về đúng định dạng JSON
- JSON phải có 2 trường:

post_title

post_content

Bước 5: Chạy thử

Bấm:```Execute step```

Nếu thành công, Gemini sẽ trả về dữ liệu ở cột OUTPUT.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a035189c-36b5-44f4-8aa1-8b9f2b0d0b4a" />

**5.2 Kiểm tra output của Gemini**

Sau khi chạy node Message a model,thấy output dạng:

```
{
  "content": {
    "parts": [
      {
        "text": "{\"post_title\":\"...\",\"post_content\":\"...\"}"
      }
    ]
  }
}
```
Điều này có nghĩa là Gemini đang trả về một chuỗi JSON nằm trong:

```
content.parts[0].text
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7c85cdf3-6a60-47f0-8c8a-ad7dcaeb293a" />

**5.3. Cấu hình node Code in JavaScript**

Node này dùng để tách JSON mà Gemini trả về thành dữ liệu dễ dùng hơn.

Bước 1: Thêm node Code
Thêm node Code in JavaScript sau node Gemini

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/064ab991-e5e1-41f7-9e18-d81058a0a66b" />
Bước 2: Xóa code cũ
Xóa toàn bộ code đang có trong node Code

Bước 3: Dán code sau
```
const rawText = $input.first().json.content.parts[0].text;
const cleanData = JSON.parse(rawText);

return {
  title: cleanData.post_title,
  content: cleanData.post_content
};
```
Giải thích code:
rawText: lấy chuỗi JSON do Gemini trả về
JSON.parse(rawText): chuyển chuỗi JSON thành object
return: trả lại 2 trường:
title
content
Bước 4: Chạy node Code
Bấm Execute previous nodes
Bấm Execute step
Nếu đúng, output sẽ có:

title
content
Ví dụ:

title: Bài viết mẫu WordPress cơ bản
content: nội dung HTML của bài viết

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/840138ee-96b1-4783-9e83-a669e4ba4d7c" />

8.7. Cấu hình WordPress Create a Post
Node này dùng để đăng bài viết lên WordPress.

Bước 1: Thêm node WordPress
Chọn node WordPress
Chọn operation Create a Post

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0357d84b-5e42-4b61-bc3f-a6f50cd9cc7d" />
Bước 2: Tạo credential WordPress
Bấm Set up credential rồi điền:

Username: tên user WordPress thật
Password: Application Password của WordPress
WordPress URL: ví dụ
Text
https://blog.luongvanhoc.io.vn/

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/df6d7551-fba6-4eed-93cb-73d885acd603" />
Bước 3: Lưu credential
Bấm Save

Lưu ý: mật khẩu ở đây không phải mật khẩu đăng nhập thông thường, mà phải là Application Password lấy trong trang profile user WordPress.

8.8. Nếu chưa có Application Password
Bạn đã kiểm tra trong WordPress như sau:

Bước 1: Vào Users
Trong WordPress admin:

vào Users
chọn user admin
Bước 2: Vào Profile
Trong trang profile của user admin, tìm mục:

Application Passwords
Bước 3: Tạo Application Password mới
nhập tên ví dụ: n8n
bấm Add New Application Password
WordPress sẽ sinh ra một chuỗi mật khẩu mới.

Bước 4: Copy chuỗi đó
Dán chuỗi này vào credential WordPress trong n8n.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f47e429e-98a6-4c8e-96f9-72aa882532a5" />





8.9. Cấu hình các trường trong node WordPress
Trong node Create a Post, bạn đã điền:

Title
Text
{{$json.title}}
Content
Text
{{$json.content}}
Status
Text
Publish
Giải thích:
{{$json.title}}: lấy tiêu đề từ node Code
{{$json.content}}: lấy nội dung từ node Code
Publish: đăng bài ngay sau khi tạo

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a4dee60f-bd51-458c-9f00-d376531c355c" />













Bước 2: Thêm node Google Gemini - Message a Model







<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9d28bfb3-61e9-4bd2-81d8-df5d3fe6140b" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ebfd5b8d-c051-4652-98f2-dd1b645e6595" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/39409975-d60e-4307-a031-0d1204bf219b" />


























