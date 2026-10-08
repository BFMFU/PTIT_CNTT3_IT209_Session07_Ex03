# Bài 3: Thiết lập MySQL và Systemd cho Spring Boot

## 1. Mục tiêu

- Khởi tạo database MySQL và phân quyền người dùng.
- Tạo tài khoản Linux `spring-runner` không có shell login.
- Tự xây dựng dịch vụ Systemd để quản lý ứng dụng Spring Boot.
- Đảm bảo ứng dụng chạy dưới quyền non-root và sử dụng cổng 8082.

## 2. Cấu hình database

- Database: `springboot_db`
- MySQL user: `spring-admin`
- Host: `localhost`
- Quyền: Toàn quyền trên database `springboot_db`.

## 3. Cấu hình Systemd

Tệp cấu hình: `/etc/systemd/system/spring-app.service`

- User chạy ứng dụng: `spring-runner`
- Đường dẫn ứng dụng: `/opt/spring-app/app.jar`
- Lệnh chạy: `/usr/bin/java -jar /opt/spring-app/app.jar`
- Cổng ứng dụng: `8082`
- Chính sách khởi động lại: `Restart=on-failure`
- Thời gian chờ khởi động lại: 10 giây.

## 4. Kết quả kiểm tra

### 4.1. Trạng thái dịch vụ

Lệnh thực hiện:

```bash
sudo systemctl status spring-app.service
```

Kết quả thực tế:
```
root@azvps-dynamic:~# sudo systemctl status spring-app.service
● spring-app.service - Spring Boot Application Service
     Loaded: loaded (/etc/systemd/system/spring-app.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2026-10-08 21:08:14 +07; 4min 0s ago
   Main PID: 150885 (java)
      Tasks: 29 (limit: 2237)
     Memory: 163.2M
        CPU: 7.497s
     CGroup: /system.slice/spring-app.service
             └─150885 /usr/bin/java -jar /opt/spring-app/app.jar

Oct 08 21:08:17 azvps-dynamic java[150885]:  =========|_|==============|___/=/_/_/_/
Oct 08 21:08:17 azvps-dynamic java[150885]:  :: Spring Boot ::                (v4.1.1)
Oct 08 21:08:17 azvps-dynamic java[150885]: 2026-10-08T21:08:17.975+07:00  INFO 150885 --- [app] [           main] ra.edu.app.AppApplication                : Starting AppApplication v0.0.1->
Oct 08 21:08:17 azvps-dynamic java[150885]: 2026-10-08T21:08:17.985+07:00  INFO 150885 --- [app] [           main] ra.edu.app.AppApplication                : No active profile set, falling >
Oct 08 21:08:20 azvps-dynamic java[150885]: 2026-10-08T21:08:20.412+07:00  INFO 150885 --- [app] [           main] o.s.boot.tomcat.TomcatWebServer          : Tomcat initialized with port 80>
Oct 08 21:08:20 azvps-dynamic java[150885]: 2026-10-08T21:08:20.462+07:00  INFO 150885 --- [app] [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
Oct 08 21:08:20 azvps-dynamic java[150885]: 2026-10-08T21:08:20.467+07:00  INFO 150885 --- [app] [           main] o.apache.catalina.core.StandardEngine    : Starting Servlet engine: [Apach>
Oct 08 21:08:20 azvps-dynamic java[150885]: 2026-10-08T21:08:20.650+07:00  INFO 150885 --- [app] [           main] b.w.c.s.WebApplicationContextInitializer : Root WebApplicationContext: ini>
Oct 08 21:08:22 azvps-dynamic java[150885]: 2026-10-08T21:08:22.023+07:00  INFO 150885 --- [app] [           main] o.s.boot.tomcat.TomcatWebServer          : Tomcat started on port 8082 (ht>
Oct 08 21:08:22 azvps-dynamic java[150885]: 2026-10-08T21:08:22.051+07:00  INFO 150885 --- [app] [           main] ra.edu.app.AppApplication                : Started AppApplication in 5.593>
lines 1-20/20 (END)

```
### 4.2. Kiểm tra cổng lắng nghe

Lệnh thực hiện:

```bash
sudo ss -tlnp | grep 8082
```

Kết quả thực tế:

```
root@azvps-dynamic:~# sudo ss -tlnp | grep 8082
LISTEN 0      100                *:8082             *:*    users:(("java",pid=150885,fd=9))

```

### 4.3. Kiểm tra tài khoản thực thi

Lệnh thực hiện:

```bash
ps -eo user,pid,args | grep '[j]ava'
```

Kết quả thực tế:

```
root@azvps-dynamic:~# ps -eo user,pid,args | grep '[j]ava'
spring-+  150885 /usr/bin/java -jar /opt/spring-app/app.jar
```

## 5. Kết luận

Ghi nhận kết quả triển khai thực tế, trạng thái dịch vụ, cổng lắng nghe và tài khoản Linux thực thi ứng dụng.