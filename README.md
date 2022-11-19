
# A Java Maven RESTful Web App

## 1. Create a Java RESTFul web app by Maven
```console
$ cd code
$ mvn archetype:generate -DgroupId=xyz.javaneverdie.calculator -DartifactId=calculatorwebapp -DarchetypeGroupId=org.glassfish.jersey.archetypes -DarchetypeArtifactId=jersey-quickstart-webapp -DarchetypeVersion=3.1.0 -Dversion=1.0-SNAPSHOT -DinteractiveMode=false
```
>注意：经过查询 https://mvnrepository.com/ 发现 jersey-quickstart-webapp 属于 org.glassfish.jersey.archetypes，而且最新版本是 3.1.0

## 2. Modify pom.xml

### 2.1 增加源代码编码和 java compile 版本属性设定
```code
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
  </properties>
```
>重要：java compiler 版本要与实际使用的 JDK 保持一致。

### 2.2 增加 jersey-media-json-binding 依赖
使用 jersey-media-json-binding 解析 JSON 返回值
```code
        <dependency>
            <groupId>org.glassfish.jersey.media</groupId>
            <artifactId>jersey-media-json-binding</artifactId>
        </dependency>
```

### 2.3 增加 httpclient 依赖 
使用 httpclient 做集成测试
```code
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.13</version>
            <scope>test</scope>
        </dependency>       
```

### 2.4 增加 jersey-media-json-binding 依赖
使用 jersey-media-json-binding 解析 JSON 返回值
```code
        <dependency>
            <groupId>org.glassfish.jersey.media</groupId>
            <artifactId>jersey-media-json-binding</artifactId>
        </dependency>
```

### 2.5 增加 jetty-maven-plugin  
使用 jetty-maven-plugin 启动内嵌的 Jetty Server，部署项目 war 包，准备用于集成测试
```code
            <plugin>
                <groupId>org.eclipse.jetty</groupId>
                <artifactId>jetty-maven-plugin</artifactId>
                <version>11.0.12</version>
                <configuration>
                    <webApp>
                        <contextPath>/calculator</contextPath>
                    </webApp>
                    <stopPort>8079</stopPort>
                    <stopKey>stop-jetty-for-it</stopKey>
                    <stopWait>10</stopWait>
                    <httpConnector>
                        <port>9999</port>
                        <idleTimeout>60000</idleTimeout>
                    </httpConnector>
                </configuration>
                <executions>
                    <execution>
                        <id>start-jetty</id>
                        <phase>pre-integration-test</phase>
                        <goals>
                            <goal>start</goal>
                        </goals>
                        <configuration>
                            <scanIntervalSeconds>0</scanIntervalSeconds>
                            <daemon>true</daemon>
                        </configuration>
                    </execution>
                    <execution>
                        <id>stop-jetty</id>
                        <phase>post-integration-test</phase>
                        <goals>
                            <goal>stop</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```
可以运行 `mvn jetty:run` 手动启动 Jetty Server，然后访问 http://localhost:9999，发现 calculator 应用已经自动部署了。

### 2.6 增加 maven-surefire-plugin  
使用 maven-surefire-plugin 做集成测试
```code
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.2</version>
                <executions>
                    <execution>
                        <id>run-integration-test</id>
                        <phase>integration-test</phase>
                        <goals>
                            <goal>test</goal>
                        </goals>
                        <configuration>
                            <includes>
                                <include>**/*IT.java</include>
                            </includes>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
```
>说明：代码中以 IT 结尾的 .java 文件会被视为是集成测试用例测试代码。

### 3. 运行集成测试
```console
$ cd calculatorwebapp
$ mvn clean verify
...
--- maven-surefire-plugin:2.22.2:test (run-integration-test) @ calculatorwebapp ---
[INFO]
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running xyz.javaneverdie.calculator.CalculatorServiceIT
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.9 s - in xyz.javaneverdie.calculator.CalculatorServiceIT
[INFO]
[INFO] Results:
[INFO]
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0
...
```
恭喜，你成功自动化运行了集成测试！


### 3. 提交代码到 github 
这里使用 GitHub CLI 命令行方式创建 Repo

### 3.1 Download and install GitHub CLI
https://cli.github.com/

### 3.2 Generate a Personal Access Token with repo operation privilege
https://github.com/settings/tokens

创建一个 Personal access tokens (classic)，设置过期时间 30天，复制 token，后面备用

### 3.3 Create a repo
```console
$ set GH_TOKEN=1234567890
$ gh repo create java-maven-restful-web-app --public
✓ Created repository maping/java-maven-restful-web-app on GitHub
```

### 3.4 Commit source code
```console
$ cd calculatorwebapp
$ mvn clean
$ echo "# java-maven-restful-web-app" >> README.md
$ git init
$ git add -A
$ git commit -m "add java maven archetype restful app"
$ git branch -M main
$ git remote add origin https://github.com/maping/java-maven-restful-web-app.git
$ git push -u origin main
```
