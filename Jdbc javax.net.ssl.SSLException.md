# Jdbc javax.net.ssl.SSLException

报错信息

    Spring Boot: Jdbc javax.net.ssl.SSLException: closing inbound before receiving peer's close_notify

因为ssl连接数据库失败，关掉ssl就好了

    spring.datasource.url=jdbc:mysql://localhost:3306/employee_database?useSSL=false

[link](https://stackoverflow.com/questions/53131321/spring-boot-jdbc-javax-net-ssl-sslexception-closing-inbound-before-receiving-p)
