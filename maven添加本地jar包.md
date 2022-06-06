    <dependency>  
        <groupId>ojdbc6</groupId>  
        <artifactId>ojdbc6</artifactId>  
        <version>6</version>  
        <scope>system</scope>  
        <systemPath>${project.basedir}/lib/ojdbc6.jar</systemPath>  
    </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <includeSystemScope>true</includeSystemScope>
            </configuration>
            </plugin>
        </plugins>
    </build>