# Docker 安装 Redis

1. 取最新版的 Redis 镜像

        $docker pull redis:latest

2. 查看本地镜像

        $docker images

3. 运行容器

        $docker run -itd --name redis-test -p 6379:6379 redis

[Docker 安装 Redis](https://www.runoob.com/docker/docker-install-redis.html)
