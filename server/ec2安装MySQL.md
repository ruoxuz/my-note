1. 添加MySQL Yum Repository到Amazon Linux 2
    
        sudo yum install https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm

2. 安装mysql

        sudo amazon-linux-extras install epel -y
        sudo yum install mysql-community-server

3. start MySQL

        sudo systemctl enable --now mysqld
        
4. 确认MySQL运行状态

        systemctl status mysqld

5. 查看root的初始密码

        sudo grep 'temporary password' /var/log/mysqld.log
        
6. 用这个密码强化服务

        sudo mysql_secure_installation -p'BEw-U?DV,7eO'
        
[原文](https://techviewleo.com/how-to-install-mysql-8-on-amazon-linux-2/)