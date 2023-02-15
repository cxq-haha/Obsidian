
# Maven异常
Could not transfer artifact javax.servlet:javax.servlet-api:pom:3.1.0 from/to central (https://repo.maven.apache.org/maven2): Transfer failed for https://repo.maven.apache.org/maven2/javax/servlet/javax.servlet-api/3.1.0/javax.servlet-api-3


在使用idea时，pom文件报错，是因为jar包下载不完整，第一次下载失败时会在对应jar包的文件目录下生成一个lastUpdated文件，导致以后都不会真正下载jar包
