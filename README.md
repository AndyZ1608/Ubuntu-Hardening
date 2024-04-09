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
'''
vim /etc/hosts.allow
'''
sshd : "IP of PAM"

2. 
