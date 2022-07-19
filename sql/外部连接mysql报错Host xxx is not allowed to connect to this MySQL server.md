# 外部连接mysql报错Host xxx is not allowed to connect to this MySQL server

## 查看用户的host

    mysql> select user, host from mysql.user;

## 解决办法

    mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'PASSWORD';
    mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
    mysql> FLUSH PRIVILEGES;

[Host xxx is not allowed to connect to this MySQL server の対応](https://zenn.dev/ryo_kawamata/articles/mysql-connect-error)

[How to grant all privileges to root user in MySQL 8.0](https://stackoverflow.com/questions/50177216/how-to-grant-all-privileges-to-root-user-in-mysql-8-0)
