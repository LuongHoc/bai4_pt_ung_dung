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

### Kết quả đạt được

Sau khi triển khai thành công:

- WordPress hoạt động trên ```blog.luongvanhoc.io.vn```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/51c3d0ce-856e-4e9d-95a5-87b820a40477" />

- phpMyAdmin hoạt động trên ```pma.luongvanhoc.io.vn```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c66c781e-fd82-4a67-90c7-e60b45d7c832" />

- N8n hoạt động trên ```n8n.luongvanhoc.io.vn```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d08da49f-7375-46ce-846e-cc5b4c5dfbc0" />









