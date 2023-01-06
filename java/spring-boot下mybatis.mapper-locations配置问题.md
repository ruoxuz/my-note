一、作用
用于将配置路径下的*.xml文件加载到mybatis中

二、如何配置
springboot或者spring项目经常会引用其它项目，把其它项目的Jar包加进来，因为每个项目的包路径不一样,mapper.xml的路径也不一样，这个时候就需要引入多个路径。

1. *.xml文件路径在*resources包*下时，可根据路径配置如下

 方法一：只有一个路径

    mybatis.mapper-locations= classpath:mapper/*.xml
 ​
 方法二：有多个路径
 
    mybatis.mapper-locations= classpath:mapper/*.xml,classpath:mapper/user*.xml
 ​
 方法三：通配符 ** 表示任意级的目录
 
    mybatis.mapper-locations= classpath:**/*.xml

2. *.xml文件路径在*java包*下时，不可使用mybatis.mapper-locations配置，可根据路径配置如下

在pom.xml的<build>标签中添加如下

    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>

 https://www.cnblogs.com/lhw-BC/p/14377527.html