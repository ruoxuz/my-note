# docker install jenkins

1. 新建Jenkins挂载目录

        mkdir  -p /wf-data/jenkins_home

2. 启动

        docker run \
        -u root \
        --name jenkins \
        -d \
        -e JVM_XMS=1G \
        -e JVM_XMX=2G \
        -p 8080:8080 \
        -p 50000:50000 \
        -v /wf-data/jenkins_home:/var/jenkins_home \
        -v /var/run/docker.sock:/var/run/docker.sock \
        -v /usr/bin/docker:/usr/bin/docker \
        jenkins/jenkins:2.375.2-lts-jdk17
