**Bảo mật SSH cho server Linux**

1. Limit hosts.allow và hosts.deny SSHD chỉ vào từ PAM
    Config vào 2 file /etc/hosts.deny và /etc/hosts.allow , ví dụ:
    - file /etc/hosts.deny : với sshd thì cấm tất
    ```
    nano /etc/hosts.deny
    ```
    ```
    sshd : ALL
    ```
    - file /etc/hosts.allow : chỉ cho phép truy cập với PAM
    ```
    nano /etc/hosts.allow
    ```
    ```
    sshd : "IP of PAM"
    ```
2. SSHD giới hạn chỉ cho authen bằng SSH Key only, chỉ cho ssh từ pam.operator
    Bình thường ta sẽ login ssh bằng cách nhập password, nhưng để nâng cao tính bảo mật thì ta nên chuyển qua authen bằng public key. Config như sau trong /etc/ssh/sshd_config để đổi từ password sang public key
    ```
    nano /etc/ssh/sshd_config
    ```
    Thêm vào cuối dòng
    ```
    PasswordAuthentication no 
    ChallengeResponseAuthentication no 
    PermitEmptyPasswords no 
    PubkeyAuthentication yes
    PermitRootLogin no
    AllowUsers pam.operator
    ```
  
3. Tạo normal user pam.operator  và add ssh public key cho user pam.operator
    ```
    sudo adduser --force-badname pam.operator
    ```
    ```
    sudo visudo
    ```
    Ở phần 
    `# User privilege specification
        root    ALL=(ALL:ALL) ALL    `
   Thêm user vào
   ```
   pam.operator    ALL=(ALL:ALL) ALL
   ```
    Nhập password cùng các thông tin cho user
    Tiếp theo, thêm khóa công khai SSH cho người dùng "pam.operator". Đầu tiên, hãy tạo một thư mục .ssh trong thư mục home của người dùng bằng lệnh:
    ```
    sudo mkdir /home/pam.operator/.ssh
    ```
    Bây giờ, hãy tạo một tệp tin mới có tên authorized_keys trong thư mục .ssh:
    ```
    sudo touch /home/pam.operator/.ssh/authorized_keys
    ```
    Bạn sẽ được admin gửi cho khóa của PAM theo dạng public key.
    Sao chép toàn bộ nội dung khóa công khai được hiển thị trên terminal.
    Tiếp theo, thêm khóa công khai vào tệp tin authorized_keys của người dùng "pam.operator".
    Mở tệp tin authorized_keys để chỉnh sửa:
    ```
    sudo nano /home/pam.operator/.ssh/authorized_keys
    ```
    Trong trình soạn thảo nano, dán nội dung khóa công khai vào cuối tệp tin authorized_keys. Sau khi dán xong, nhấn Ctrl + X để thoát, sau đó nhấn Y và Enter để lưu thay đổi.
    Đảm bảo rằng chủ sở hữu của thư mục .ssh và tệp tin authorized_keys là người dùng "pam.operator":
    ```
    sudo chown -R pam.operator:pam.operator /home/pam.operator/.ssh
    ```
    Cấp quyền truy cập cho thư mục .ssh và tệp tin authorized_keys
    ```
    sudo chmod 700 /home/pam.operator/.ssh
    sudo chmod 600 /home/pam.operator/.ssh/authorized_keys
    ```
    Khởi động lại SSH
   ```
   sudo systemctl restart ssh
   ```
5. Đổi pass fciadmin, root, pam.operator về 1 password mới
   ```
   sudo passwd fciadmin
   ```
   ```
   sudo passwd pam.operator
   ```
   ```
   sudo passwd root
   ```
6. List toàn bộ account local
   Sử dụng lệnh sau để liệt kê toàn bộ tài khoản người dùng:
   ```
   cut -d: -f1 /etc/passwd | awk '{print $0}'
   ```
