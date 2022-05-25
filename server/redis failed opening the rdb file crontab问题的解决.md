错误信息

    835:M 08 Dec 11:32:57.030 * 1 changes in 900 seconds. Saving...
    835:M 08 Dec 11:32:57.030 * Background saving started by pid 18114
    18114:C 08 Dec 11:32:57.031 # Failed opening the RDB file crontab (in server root dir /etc) for saving: Permission denied
    835:M 08 Dec 11:32:57.130 # Background saving error
    
打开redis的配置文件（vi /etc/redis/redis.conf）

    # The working directory.
    #
    # The DB will be written inside this directory, with the filename specified
    # above using the 'dbfilename' configuration directive.
    #
    # The Append Only File will also be created inside this directory.
    #
    # Note that you must specify a directory here, not a file name.
    dir /var/lib/redis

利用redis的客户端（redis-cli）查看

    127.0.0.1:6379> CONFIG GET dir
    1) "dir"
    2) "/etc"

发现和配置文件中不一样

于是执行


    config set dir /var/lib/redis
    
将dir 配置设置回去，问题解决。

[link](https://blog.csdn.net/zhangjunli/article/details/103817837)