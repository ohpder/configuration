### Pom文件配置

```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>1.0.0</version>

    <!--将插件绑定在某个phase执行-->
    <executions>
        <execution>
            <id>build-image</id>
            <!--将插件绑定在package这个phase上。也就是说，用户只需执行mvn package ，就会自动执行mvn docker:build-->
            <phase>package</phase>
            <goals>
                <goal>build</goal>
            </goals>
        </execution>
    </executions>

    <configuration>
        <!--指定生成的镜像名-->
        <imageName>ohp/${project.artifactId}</imageName>
        <!--指定标签-->
        <imageTags>
            <imageTag>latest</imageTag>
        </imageTags>
        <!-- 指定 Dockerfile 路径-->
        <dockerDirectory>${project.basedir}/src/main/resources</dockerDirectory>

        <!--指定远程 docker api地址-->
        <dockerHost>http://172.30.21.66:2375</dockerHost>

        <!-- 这里是复制 jar 包到 docker 容器指定目录配置 -->
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <!--jar 包所在的路径  此处配置的 即对应 target 目录-->
                <directory>${project.build.directory}</directory>
                <!-- 需要包含的 jar包 ，这里对应的是 Dockerfile中添加的文件名　-->
                <include>${project.build.finalName}.jar</include>
            </resource>
        </resources>
    </configuration>
</plugin>
```

### Dockfile

```dockerfile
FROM java:8
VOLUME /tmp
ADD *.jar app.jar
ENV TZ 'Asia/Shanghai'
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
RUN bash -c 'touch /app.jar'
EXPOSE 8003
ENTRYPOINT [ "java", "-Djava.security.egd=file:/dev/./urandom","-Dfile.encoding=UTF-8","-Dsun.jnu.encoding=UTF-8", "-jar", "/app.jar" ]
```