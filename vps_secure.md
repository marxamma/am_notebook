# 密码
- 使用8位以上的密码，同时使用大小写字母、数字、特殊字符
- 尽量不要使用同一个密码
- 不要使用生日或电话等个人信息作为密码
- 定期更换密码

# 禁用root用户ssh登录
1. 添加一个用户marx
> useradd marx
2. 为用户marx设置密码
> passwd marx
3. 将用户marx添加到sudoers中
   1. 使用vim打开以下文件
   > vim /etc/sudoers
   2. 在文件中增加以下内容
   > marx    ALL=(ALL)       ALL  
   3. 保存修改，提示只读文件的话，使用wq!强制保存
4. 启用防火墙
   1. 清空原有防火墙规则
    > iptables -F  
    > iptables -X  
    > iptables -Z  
   2. 配置新的防火墙规则
    > iptables -A INPUT -p tcp -m multiport --dport 10086,80,21,22 -j ACCEPT #允许SSH端口10086、WEB端口80、FTP端口21、22开放
   3. 保存配置
    > iptables-save
5. 更改ssh配置
   1. 打开ssh配置文件
    > vim /etc/ssh/sshd_config
   2. 增加一个新端口
    > Port 10086 #ssh端口尽量设置大一点
   3. 重启ssh服务
    > service sshd restart
   4. 使用新端口登陆，如果能够正常登陆，则进入下一步，否则关闭防火墙
   5. 重新打开ssh配置文件
    > vim /etc/ssh/sshd_config
   6. 修改以下配置
    > PermitRootLogin no #禁止root登录  
    > X11Forwarding no #禁止X11转发  
    > PermitEmptyPasswords no #禁止空密码登录  
    > AllowUsers marx #用户marx可以登录  
   7. 重启ssh服务
    > service sshd restart
   8. 使用marx用户和10086端口尝试登陆

# 安装宝塔免费防火墙
1. 注册宝塔账户
2. 在宝塔面板中登录
3. 点击购买插件，安装防火墙

---

# 删除不使用的用户和用户组（未经验证，谨慎选择）
Linux默认自带了一些用户和用户组，我们基本用不到，可以直接删除
> userdel adm  
> userdel lp  
> userdel sync
> userdel shutdown  
> userdel halt  
> userdel news  
> userdel uucp  
> userdel operator  
> userdel games  
> userdel gopher  
> userdel ftp #若您需要使用匿名FTP请保留此用户，否则删除  
> groupdel adm  
> groupdel lp  
> groupdel news  
> groupdel uucp  
> groupdel games  
> groupdel dip  
> groupdel pppusers

# 限制su命令（可选）
1. 打开配置文件/etc/pam.d/su
> vim /etc/pam.d/su
2. 增加以下两行
> auth sufficient /lib/security/pam_rootok.so debug  
> auth required /lib/security/pam_wheel.so group=isd  
3. 执行以下命令
> usermod -G10 marx #只有marx用户可以使用su命令  

# 禁止ping（可选）
> echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all  

# 使用webshell后门查杀（可选）
https://scanner.baidu.com/

# 使用百度的OpenRASP（可选）
https://rasp.baidu.com/
