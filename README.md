# React + Vite
## Triển khai CI/CD dự án
Đầu tiền cần chuẩn bị máy chủ để cài đặt github runner
Dựa theo cấu hình trước, chúng ta không thể truy cập thẳng vào Server Frontend mà phải thông qua Basion
1. SSH vào Server Basion
```
ssh -i "key.pem" ubuntu@< địa chỉ của máy chủ >
```
Nếu gặp lỗi về quyền trong quá trình SSH, hãy phần quyền thêm cho key.pem
```
chmod 400 "key.pem"
```
2. Sau khi đã SSH vào Basion ta tiếp tục SSH vào frontend ( backend tương tự như vậy)
3. Tiến hành cài đặt Github Action trên Server
- Truy cập vào Repository của mình để tiến hành cài đặt
- Chọn Setting -> Action -> Runner -> New self-hosted runner
4. Tiến hành thực hiện từng bước theo hướng dẫn trên server frontend
Ví dụ:
```
$ mkdir actions-runner; cd actions-runner
$ Invoke-WebRequest -Uri https://github.com/actions/runner/releases/download/v2.319.1/actions-runner-win-x64-2.319.1.zip -OutFile actions-runner-win-x64-2.319.1.zip
$ if((Get-FileHash -Path actions-runner-win-x64-2.319.1.zip -Algorithm SHA256).Hash.ToUpper() -ne '1c78c51d20b817fb639e0b0ab564cf0469d083ad543ca3d0d7a2cdad5723f3a7'.ToUpper()){ throw 'Computed checksum did not match' }
$ Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD/actions-runner-win-x64-2.319.1.zip", "$PWD")
$ ./config.cmd --url https://github.com/tranhoangvu0106/market --token BI2JGF2KNBDKHGNUCKUDBELGZRMGK
$ ./run.cmd
```
Ở bước cuối cùng nếu chúng ta muốn chạy nền hãy chạy câu lệnh
```
$ sudo ./svc.sh install
$ sudo ./svc.sh start
```
5. Tiến hành tạo file .yaml để cài đặt
Chọn Action -> New workflow -> Continuous integration ( Nodejs)
6. Tiến hành chỉnh sửa và commit file .yaml

7. Ở bước cuối cùng chúng ta sẽ copy hết tất cả các file được build ra ở thư mục dist vào /var/www/html/
8. Cấu hình file .conf để có thể chạy dự án
File tham khảo
- Tạo file
```
 sudo nano /etc/apache2/sites-available/market.conf
```
- Cấu hình file tham khảo
```
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    ServerName localhost
    # Cấu hình tùy chọn
    <Directory /var/www/html>
        allow from all
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
- Lưu lại file
9. Sau khi tạo file hay chỉnh sửa, tiến hành restart lại apache2
```
sudo systemctl restart apache2
```

