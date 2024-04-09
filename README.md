###Bảo mật SSH cho server Linux 

1. Limit hosts.allow và hosts.deny SSHD chỉ vào từ PAM
Config vào 2 file /etc/hosts.deny và /etc/hosts.allow , ví dụ:
- file /etc/hosts.deny : với sshd thì cấm tất
```
vim /etc/hosts.deny
```
```
sshd : ALL
```
- file /etc/hosts.allow : chỉ cho phép truy cập với PAM
```
vim /etc/hosts.allow
```
```
sshd : "IP of PAM"
```
2. SSHD giới hạn chỉ cho authen bằng SSH Key only, chỉ cho ssh từ pam.operator
Bình thường ta sẽ login ssh bằng cách nhập password, nhưng để nâng cao tính bảo mật thì ta nên chuyển qua authen bằng public key. Config như sau trong /etc/ssh/sshd_config để đổi từ password sang public key
```
vim /etc/ssh/sshd_config
```
```
PasswordAuthentication no # ko sử dụng authen bằng password
ChallengeResponseAuthentication no # ko sử dụng authen bằng password
PermitEmptyPasswords no # ko cho phép truy cập bằng password rỗng (đối với user ko setup pass)
RSAAuthentication no # Không authen RSA（version 1 thôi）
PubkeyAuthentication yes # sử dụng public key để authen
```
