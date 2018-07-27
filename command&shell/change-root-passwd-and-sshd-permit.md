# Ubuntu下更新Root密码并开启sshd远程登录
echo username:password | sudo chpasswd root
sudo sed -i 's/^.*\(PermitRootLogin\)\s*no/\1 yes/g' /etc/ssh/sshd_config
sudo sed -i 's/^.*\(PasswordAuthentication\)\s*no/\1 yes/g' /etc/ssh/sshd_config