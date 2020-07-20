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

## ログ出力

出力パス
```
./opt/IBM/WebSphere/AppServer/profiles/AppSrv01/logs/testlog.log
```

## log4j2.xmlパス変更

~~設定ファイルの類はsrc/main/resourcesに配置すれば動くが、下記の方法でも動く。~~  
~~間違い。動かない。前回コンパイル時に残っていた残骸のおかげて動いていた。~~  
動かなかった原因判明。ライブラリが足りない。下記3つが必要。  
web.xmlにパスを記載するにはlog4j-webが必要。
```
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-api</artifactId>
      <version>2.12.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-core</artifactId>
      <version>2.12.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-web</artifactId>
      <version>2.12.1</version>
    </dependency>

```

### log4j2.xmlパス

- 変更前：./src/main/resources/log4j2.xml
- 変更後：./src/main/webapp/WEB-INF/log4j2.xml

### web.xml

下記を追記。
```
  <context-param>
    <param-name>log4jConfiguration</param-name>
    <param-value>/WEB-INF/log4j2.xml</param-value>
  </context-param>
```
※techscoreの記事によるとweb-appタブ直下に記載するものらしいが、どこに記載しても動く。~

## 参考

[はてなブログ : CLOVER : Apache MavenでWebアプリにトライ](https://kazuhira-r.hatenablog.com/entry/20110520/1305895216)  
元ネタは[これ](http://maven.apache.org/guides/mini/guide-webapp.html)らしいがNotFoundになっている。

[techscore : 3. サーブレットの基本2](https://www.techscore.com/tech/Java/JavaEE/Servlet/3/)  
[Qiita : log4j2 を Webアプリケーションで使う場合の配置と設定](https://qiita.com/pica/items/afcebf0a06a745cb8c8c)

