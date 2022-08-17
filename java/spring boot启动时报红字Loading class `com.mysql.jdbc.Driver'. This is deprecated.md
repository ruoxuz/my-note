# spring boot启动时报红字Loading class `com.mysql.jdbc.Driver'. This is deprecated

在application.properties里添加

    spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

使用了log4j2也可能会导致这个问题
在log4jdbc.log4j2.properties里添加

    log4jdbc.auto.load.popular.drivers=false
    log4jdbc.drivers=com.mysql.cj.jdbc.Driver
