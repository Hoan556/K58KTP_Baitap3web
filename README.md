# K58KTP_Baitap3web
Bài tập 3   : môn Phát triển ứng dụng trên nền web
Giảng viên  : Đỗ Duy Cốp
Lớp học phần: 58KTPM
Ngày giao   : 2025-10-24 13:50
Hạn nộp     : 2025-11-05 00:00
--------------------------------------------------
Yêu cầu     : LẬP TRÌNH ỨNG DỤNG WEB trên nền linux
1. Cài đặt môi trường linux: SV chọn 1 trong các phương án
 - enable wsl: cài đặt docker desktop
 - enable wsl: cài đặt ubuntu
 - sử dụng Hyper-V: cài đặt ubuntu
 - sử dụng VMware : cài đặt ubuntu
 - sử dụng Virtual Box: cài đặt ubuntu
2. Cài đặt Docker (nếu dùng docker desktop trên windows thì nó có ngay)
3. Sử dụng 1 file docker-compose.yml để cài đặt các docker container sau: 
   mariadb (3306), phpmyadmin (8080), nodered/node-red (1880), influxdb (8086), grafana/grafana (3000), nginx (80,443)
4. Lập trình web frontend+backend:
 SV chọn 1 trong các web sau:
 4.1 Web thương mại điện tử
 - Tạo web dạng Single Page Application (SPA), chỉ gồm 1 file index.html, toàn bộ giao diện do javascript sinh động.
 - Có tính năng login, lưu phiên đăng nhập vào cookie và session
   Thông tin login lưu trong cơ sở dữ liệu của mariadb, được dev quản trị bằng phpmyadmin, yêu cầu sử dụng mã hoá khi gửi login.
   Chỉ cần login 1 lần, bao giờ logout thì mới phải login lại.
 - Có tính năng liệt kê các sản phẩm bán chạy ra trang chủ
 - Có tính năng liệt kê các nhóm sản phẩm
 - Có tính năng liệt kê sản phẩm theo nhóm
 - Có tính năng tìm kiếm sản phẩm
 - Có tính năng chọn sản phẩm (đưa sản phẩm vào giỏ hàng, thay đổi số lượng sản phẩm trong giỏ, cập nhật tổng tiền)
 - Có tính năng đặt hàng, nhập thông tin giao hàng => được 1 đơn hàng.
 - Có tính năng dành cho admin: Thống kê xem có bao nhiêu đơn hàng, call để xác nhận và cập nhật thông tin đơn hàng. chuyển cho bộ phận đóng gói, gửi bưu điện, cập nhật mã COD, tình trạng giao hàng, huỷ hàng,...
 - Có tính năng dành cho admin: biểu đồ thống kê số lượng mặt hàng bán được trong từng ngày. (sử dụng grafana)
 - backend: sử dụng nodered xử lý request gửi lên từ javascript, phản hồi về json.
 4.2 Web IOT: Giám sát dữ liệu IOT.
 - Tạo web dạng Single Page Application (SPA), chỉ gồm 1 file index.html, toàn bộ giao diện do javascript sinh động.
 - Có tính năng login, lưu phiên đăng nhập vào cookie và session
   Thông tin login lưu trong cơ sở dữ liệu của mariadb, được dev quản trị bằng phpmyadmin, yêu cầu sử dụng mã hoá khi gửi login.
   Chỉ cần login 1 lần, bao giờ logout thì mới phải login lại.
 - hiển thị giá trị mới nhất của các thông số đang giám sát, khi click vào thì hiển thị đồ thị lịch sử quá trình thay đổi (gọi grafana iframe để hiển thị)
 - backend: Sử dụng nodered để đọc dữ liệu từ các cảm biến (có thể dùng api online để lấy dữ liệu theo giời gian thực), 
   nodered sẽ lưu dữ liệu mới nhất (dạng update) vào cơ sở dữ liệu mariadb (sử dụng phpmyadmin để tạp table và quản trị lần đầu)
   nodered sẽ lưu dữ liệu (insert) vào influxdb để lưu giá trị lịch sử, để cho grafana dùng để hiển thị biểu đồ.
5. Nginx làm web-server
 - Cấu hình nginx để chạy được website qua url http://fullname.com  (thay fullname bằng chuỗi ko dấu viết liền tên của bạn)
 - Cấu hình nginx để http://fullname.com/nodered truy cập vào nodered qua cổng 80, (dù nodered đang chạy ở port 1880)
 - Cấu hình nginx để http://fullname.com/grafana truy cập vào grafana qua cổng 80, (dù grafana đang chạy ở port 3000)
# Bài làm
## 1. Cài đặt môi trường linux sử dụng VMware: cài đặt ubuntu

   <img width="433" height="433" alt="image" src="https://github.com/user-attachments/assets/9a86f6e4-692e-40a3-a5f2-1d63e7334718" />
   
## 2 . Cài đặt docker trong ubuntu
   1. Gỡ bỏ phiên bản Docker cũ (nếu có)

Chạy lệnh:

sudo apt remove docker docker-engine docker.io containerd runc -y

 2. Cập nhật hệ thống
sudo apt update
sudo apt upgrade -y

 3. Cài đặt các gói phụ trợ
sudo apt install ca-certificates curl gnupg lsb-release -y

 4. Thêm Docker’s official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

 5. Thêm repository Docker vào danh sách APT
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

 6. Cập nhật lại danh sách gói
sudo apt update

 7. Cài đặt Docker Engine, CLI và Containerd
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

 8. Kiểm tra Docker đã hoạt động chưa
sudo systemctl status docker


(ấn q để thoát)

Hoặc chạy thử:

sudo docker run hello-world


Nếu hiện dòng:

“Hello from Docker!”
→ là bạn đã cài đặt thành công 

👤 9. (Tuỳ chọn) Cho phép chạy Docker không cần sudo
sudo usermod -aG docker $USER


Sau đó đăng xuất rồi đăng nhập lại (hoặc reboot máy).

Kiểm tra:

docker ps


→ Nếu không báo lỗi “permission denied” là OK.

 10. Cài thêm Docker Compose (nếu cần bản riêng)

Nếu bạn cần file docker-compose.yml chạy bằng docker-compose (không phải plugin):

sudo apt install docker-compose -y


Kiểm tra phiên bản:

docker-compose version
<img width="1086" height="1070" alt="Ảnh chụp màn hình 2025-11-05 000233" src="https://github.com/user-attachments/assets/d7e1d477-f84c-4d06-9e10-f21ed126e451" />

## 3. Sử dụng 1 file docker-compose.yml để cài đặt các docker container sau:
```
version: '3'

services:
  mariadb:
    image: mariadb
    container_name: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: 123456        # Mật khẩu root MariaDB
      MYSQL_DATABASE: example_db                  # Tên database mặc định
      MYSQL_USER: example_user                    # Tạo user riêng
      MYSQL_PASSWORD: 123456           # Mật khẩu cho user riêng
    ports:
      - "3306:3306"
    volumes:
      - mariadb_data:/var/lib/mysql               # Lưu dữ liệu MariaDB
    restart: always

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    environment:
      PMA_HOST: mariadb
      PMA_PORT: 3306
      PMA_USER: example_user                      # Dùng user riêng để đăng nhập
      PMA_PASSWORD: 123456
    ports:
      - "8080:80"
    depends_on:
      - mariadb
    restart: always

  nodered:
    image: nodered/node-red
    container_name: nodered
    ports:
      - "1880:1880"
    volumes:
      - nodered_data:/data
    environment:
      - NODE_RED_USERNAME=admin                   # Thêm user đăng nhập Node-RED
      - NODE_RED_PASSWORD=admin123                # Mật khẩu Node-RED
      - FLOWS=nodered
    restart: always

  influxdb:
    image: influxdb
    container_name: influxdb
    environment:
      INFLUXDB_DB: example_db
      INFLUXDB_ADMIN_USER: admin
      INFLUXDB_ADMIN_PASSWORD: admin_password
      INFLUXDB_USER: influx_user                  # User riêng cho InfluxDB
      INFLUXDB_USER_PASSWORD: "12345"
    ports:
      - "8086:8086"
    volumes:
      - influxdb_data:/var/lib/influxdb
    restart: always

  grafana:
    image: grafana/grafana
    container_name: grafana
    environment:
       GF_SERVER_ROOT_URL: "%(protocol)s://nguyenvanhoan.com/grafana/"
       GF_SECURITY_ADMIN_USER: admin
       GF_SECURITY_ADMIN_PASSWORD: 123456
       GF_SERVER_SERVE_FROM_SUB_PATH: "false"
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
    depends_on:
      - influxdb
    restart: always

  nginx:
    image: nginx
    container_name: nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf        # File cấu hình nginx tùy chỉnh
      - /home/hoan/html:/usr/share/nginx/html
    depends_on:
      - mariadb
      - phpmyadmin
      - nodered
      - influxdb
      - grafana
    restart: always

volumes:
  mariadb_data:
  nodered_data:
  influxdb_data:
  grafana_data:
```
## 4.2. Web IOT: Giám sát dữ liệu IOT.
Tạo cơ sở dữ liệu trong phpMyAdmin
<img width="962" height="1002" alt="Ảnh chụp màn hình 2025-11-06 164025" src="https://github.com/user-attachments/assets/efce7936-c9e5-4386-b11f-eaee1b935c9c" />
Tạo Nodered để kết nối với MariaDB
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3cb06869-1907-4357-a34c-191a912fd24f" />




























































## 5.CẤU HÌNH NGINX
File nginx/default.conf:
```
user  nginx;
worker_processes  auto;

events {
    worker_connections 1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;
    server_tokens off;

    server {
        listen 80;
        server_name nguyenvanhoan.com www.nguyenvanhoan.com;

        # Website chính
        location / {
            root /usr/share/nginx/html;
            index index.html;
            try_files $uri $uri/ =404;
        }

        # Node-RED
        location /nodered/ {
            proxy_pass http://nodered:1880/;  # Chú ý dấu / ở cuối
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_cache_bypass $http_upgrade;
        }

        # Grafana
        location /grafana/ {
            proxy_pass http://grafana:3000/;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    }
}
````
Website chính :http://nguyenvanhoan.com/

<img width="801" height="792" alt="Ảnh chụp màn hình 2025-11-06 102832" src="https://github.com/user-attachments/assets/66b28f34-76cc-4f10-869b-5420d7704aad" />

Node-RED:http://nguyenvanhoan.com/nodered

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fc783eaa-df10-4bcb-9391-58ace9e56475" />

Grafana: http://nguyenvanhoan.com/grafana

<img width="825" height="805" alt="Ảnh chụp màn hình 2025-11-06 104217" src="https://github.com/user-attachments/assets/0fcc8cd5-95f0-499e-9d98-901bb78996aa" />
<img width="1083" height="1075" alt="Ảnh chụp màn hình 2025-11-06 105133" src="https://github.com/user-attachments/assets/cd61048b-6f00-4073-882a-50a640fb30cd" />

## 6 Kết luận 
Qua quá trình thực hiện, em đã hiểu rõ cách cài đặt và vận hành môi trường Ubuntu trên máy ảo VMware, cũng như cách triển khai các dịch vụ IoT thông qua Docker.
Các thành phần như MariaDB, phpMyAdmin, Node-RED, InfluxDB, Grafana và Nginx được cấu hình, liên kết và hoạt động ổn định nhờ file docker-compose.yml.

Em đã nắm được:

Cách cài đặt Docker và Docker Compose để quản lý nhiều dịch vụ cùng lúc.

Cách sử dụng Node-RED để thu thập và xử lý dữ liệu cảm biến.

Cách lưu trữ dữ liệu vào InfluxDB và hiển thị biểu đồ trên Grafana.

Cách cấu hình Nginx làm web server và reverse proxy cho toàn hệ thống.

Cách xây dựng website dạng SPA, hỗ trợ đăng nhập và hiển thị dữ liệu cảm biến theo thời gian thực.

Bài làm giúp em hiểu rõ quy trình tích hợp nhiều công nghệ khác nhau để xây dựng một hệ thống IoT hoàn chỉnh — từ khâu thu thập, xử lý, lưu trữ đến hiển thị dữ liệu.
Thông qua đó, em rèn luyện kỹ năng triển khai thực tế, nâng cao khả năng làm việc với Docker, Nginx, và các công cụ giám sát dữ liệu trong các dự án thực tế.







































































