##### 查看用户的host

    mysql> select user, host from mysql.user;
    
##### 解决办法

    mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'PASSWORD';
    mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
    mysql> FLUSH PRIVILEGES;

https://zenn.dev/ryo_kawamata/articles/mysql-connect-error

https://stackoverflow.com/questions/50177216/how-to-grant-all-privileges-to-root-user-in-mysql-8-0