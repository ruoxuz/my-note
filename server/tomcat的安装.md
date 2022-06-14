# tomcat的安装

1. tomcat默认端口是8080，接下来浏览输入地址：<http://服务器公网ip:8080/>。这个页面暂时是无法访问
由于tomcat的web页面是需要安装插件的，这里继续用 tomcat-webapps 和 tomcat-admin-webapps 两个插件包。安装了插件之后webapp里才会有ROOT,menger

        yum install tomcat-webapps tomcat-admin-webapps

2. 项目里java版本与JAVA_HOME的java版本不一致的话，会导致项目无法启动，输

[Linux学习3-yum安装java和Tomcat环境](https://www.cnblogs.com/yoyoketang/p/10186513.html)
