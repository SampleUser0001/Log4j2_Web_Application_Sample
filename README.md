# Log4j2_Web_Application_Sample

## Maven

### プロジェクト作成
```
mvn -B archetype:generate \
  -DarchetypeArtifactId=maven-archetype-webapp \
  -DgroupId=com.example.log4j2 \
  -DartifactId=Log4j2_Web_Application_Sample
```

### .gitignore追記

```
/target/
```

### javaディレクトリ作成

```
mkdir src/main/java
```

### ビルド（1回目）

```
mvn clean package
```

```
ls target
```

```
ec2-user:~/environment/Log4j2_Web_Application_Sample (master) $ ls target/
classes  Log4j2_Web_Application_Sample  Log4j2_Web_Application_Sample.war  maven-archiver  maven-status
```

### pom.xml修正（servlet追加）

```
<!-- https://mvnrepository.com/artifact/javax.servlet/servlet-api -->
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>servlet-api</artifactId>
  <version>2.5</version>
  <scope>provided</scope>
</dependency>
```

### サーブレット作成

```
mkdir -p src/main/java/myapp/web
touch src/main/java/myapp/web/HelloServlet.java
```
内容は現物参照。

### web.xml修正

内容は現物参照。

### mvn clean package（2回目）

```
mvn clean package
```

### jetty導入

pom.xml修正。下記追記。

```
  <build>
    <finalName>myapp-web</finalName>
    <plugins>
      <plugin>
        <groupId>org.mortbay.jetty</groupId>
        <artifactId>jetty-maven-plugin</artifactId>
        <version>8.0.0.M2</version>
        <configuration>
          <webAppConfig>
            <contextPath>/</contextPath>
          </webAppConfig>
          <scanIntervalSeconds>10</scanIntervalSeconds>
        </configuration>
      </plugin>
    </plugins>
  </build>
```

起動。
```
mvn jetty:run
```

### 疎通確認

```
telnet localhost 8080

GET /hello
```

```
ec2-user:~/environment $ telnet localhost 8080
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
GET /hello
Hello Web App!Connection closed by foreign host.
```

## 参考

[はてなブログ : CLOVER : Apache MavenでWebアプリにトライ](https://kazuhira-r.hatenablog.com/entry/20110520/1305895216)

元ネタは[これ](http://maven.apache.org/guides/mini/guide-webapp.html)らしいがNotFoundになっている。