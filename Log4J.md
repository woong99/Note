# Log4J

## Log4J란?

- 말 그대로 로그를 기록하기 위해 사용하는 도구
- Java에서 `System.out.println()`으로 출력이 가능하나 비효율적이다.
- Log4J를 이용해 쉽게 로그를 찍을 수 있음.

---

## Spring 기준 pom.xml

```xml
 <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <org.springframework-version>5.2.3.RELEASE</org.springframework-version>
        <org.slf4j-version>1.7.30</org.slf4j-version>
    </properties>

  <dependencies>
     <!--        Logging-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${org.slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>${org.slf4j-version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${org.slf4j-version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.15</version>
            <exclusions>
                <exclusion>
                    <groupId>javax.mail</groupId>
                    <artifactId>mail</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>javax.jms</groupId>
                    <artifactId>jms</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.sun.jdmk</groupId>
                    <artifactId>jmxtools</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.sun.jmx</groupId>
                    <artifactId>jmxri</artifactId>
                </exclusion>
            </exclusions>
            <scope>runtime</scope>
        </dependency>
    <dependencies>
```

---

## Spring 기준 log4j.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM
        "http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/xml/doc-files/log4j.dtd">
<log4j:configuration>

    <!--    Appenders-->
    <appender name="console" class="org.apache.log4j.ConsoleAppender">
        <param name="Target" value="System.out"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%d %5p [%c] %m%n"/>
        </layout>
    </appender>

    <!--        *추가*-->
    <appender name="console-infolog" class="org.apache.log4j.ConsoleAppender">
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%d %5p %m%n"/>
        </layout>
    </appender>

    <!--    Application Loggers-->
    <logger name="com" additivity="false">
        <level value="debug"/>
        <appender-ref ref="console"/>
    </logger>

    <!--    3rdParty Loggers-->
    <logger name="org.springframework.core">
        <level value="info"/>
    </logger>

    <logger name="org.springframework.beans">
        <level value="info"/>
    </logger>

    <logger name="org.springframework.context">
        <level value="info"/>
    </logger>

    <logger name="org.springframework.web">
        <level value="info"/>
    </logger>
    <!-- SQL Logger -->

    <logger name="jdbc.sqltiming" additivity="false">
        <level value="warn"/>
        <appender-ref ref="console"/>
    </logger>
    <logger name="jdbc.sqlonly" additivity="false">
        <level value="info"/>
        <appender-ref ref="console"/>
    </logger>
    <logger name="jdbc.audit" additivity="false">
        <level value="warn"/>
        <appender-ref ref="console"/>
    </logger>
    <logger name="jdbc.resultset" additivity="false">
        <level value="warn"/>
        <appender-ref ref="console"/>
    </logger>
    <logger name="jdbc.resultsettable" additivity="false">
        <level value="info"/>
        <appender-ref ref="console"/>
    </logger>

    <!--    Root Logger-->
    <root>
        <priority value="info"/>
        <appender-ref ref="console"/>
    </root>
</log4j:configuration>

```
