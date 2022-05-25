properties
    
    mybatis.mapper-locations=classpath:mappers/push/*.xml,classpath*:/mappers/*.xml
    
    
yml

    mybatis:
      mapper-locations:
        - classpath:dao/*.xml
        - classpath*:com/**/mapper/*.xml
        
https://jpdebug.com/p/512148

https://github.com/macrozheng/mall/blob/30526dca557c0d4405743eaaeb40fcf00ae86bee/mall-admin/src/main/resources/application.yml