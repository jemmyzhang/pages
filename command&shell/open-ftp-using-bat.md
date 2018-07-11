# 使用bat脚本快速登录FTP服务器
由于习惯使用windows资源管理器登录FTP，平时使用的时候要反复输入地址，用户名，密码，感觉神烦。于是写了个bat脚本用于自动登录。脚本很简单：
```bat
explorer ftp://{ftp-username}:{ftp-password}@{host-address}
```

名词解释：  
`ftp-username`：FTP服务用户名。  
`ftp-password`：FTP服务密码。  
`host-address`：FTP服务所在的地址。  

例子：
```bat
explorer ftp://admin:password@127.0.0.1
```