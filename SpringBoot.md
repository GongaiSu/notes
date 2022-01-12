# SpringBoot

## pom文件的配置

```xml
<!--依赖控制 --> 
	<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.6</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- 热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>
        <!--lombok插件-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
             <scope>provided</scope><!--作用范围，在打包时不进行依赖导入-->
        </dependency>
        <!-- 配置文件中属性的提醒，需要bulid-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
        </dependency>
    </dependencies>

<!--    打包插件-->
    <build>
<!--       最终的打包名字-->
        <finalName>demo</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

## 配置文件ymal

```yaml
# 文件拆分后，使用下面的命令进行要加载的文件，如果需要加载多个文件，按照数组的方式添加。添加名为application-后面的名称
spring:
  profiles:
    active: [文件1，文件2]
```

```java
//读取application.yml文件中的属性，在application.yml中必须以括号中的名称开头的属性，使用的是方法名称的set方法注入属性

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
import java.io.Serializable;
import java.util.Map;

@Component
@ConfigurationProperties("persion")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Persion implements Serializable {

    private String name;
    private Integer age;
    private Map usermap;
}

```

```yaml
persion:
  name: zhangsan
  age: 12
  usermap: {v1: 12,v2: 13}
```

```java
//如果不在application.yml文件中添加对应类的属性，需要添加@PropertySource注解，而且中不支持yml文件，需要后续查询认证
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;
import java.io.Serializable;
import java.util.Map;

@Component
@PropertySource("classpath:application-den.properties")
@ConfigurationProperties("persion")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Persion implements Serializable {
    private String name;
    private Integer age;
    private Map usermap;
}
```

## 结合MyBatis

### pom.xml中的配置

```xml
<!--在原有的依赖上面加上这些依赖-->
<!--        mybatis依赖-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>
<!--        mysql依赖-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
<!--        druid依赖-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.2.1</version>
        </dependency>
```

### application.yml中的配置

```yaml
#数据源链接地址
spring:
  profiles:
    active: dev #每个数据源的链接的配置文件

mybatis:
  type-aliases-package: com.gyf.domain #取别名
  configuration:
    map-underscore-to-camel-case: true #开启驼峰命名法
    use-generated-keys: true #开启主键id返回,在新增数据时，把新增的数据id返回，需要在mapper.xml中添加KeyProperty="主键的名称"
server:
  servlet:
    context-path: /ect #访问路径的前缀
  port: 8090 #端口号
```

### applicatiion-dev.yml配置

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: 123456
    url: jdbc:mysql://127.0.0.1:3306/user?serverTimezone=UTC
```

## 日志处理

### Logback+SLF4J

* 启动Logback

  ```java
  //在配置pom中天机lombok插件依赖，在需要添加日志的类上面添加注解
  import lombok.extern.slf4j.Slf4j;
  
  @Slf4j
  public class SLF4J_Logback1 {
      public static void main(String[] args) {
          for (int i = 0; i < 200; i++) {
              log.info("信息");
              log.error("错误");
          }
          for (int i = 0; i < 50; i++) {
              System.out.println("111----------");
          }
      }
  }
  ```

* 配置文件

  * 在pom.xml文件中添加Logback所需要的依赖

    ```xml
    		<!--logback所需要的依赖-->
    		<dependency>
                <groupId>ch.qos.logback</groupId>
                <artifactId>logback-classic</artifactId>
            </dependency>
    ```

    

  * 在resources下添加logback.xml文件

  ```xml
  <?xml version="1.0" encoding="utf-8" ?>
  <!-- debug="true":开启logback的debug信息-->
  <!--scan="true" scanPeriod="30 seconds":开启自动扫描，每30秒自动扫描一次-->
  <configuration debug="true" scan="true" scanPeriod="30 seconds">
  <!--    抽取公共部分-->
  <!--    自动配置颜色：%highlight();手动配置颜色：%red[颜色]()-->
      <property name="CONSOLE_PATTERN" value="%d{HH:mm:ss.SSS} [%highlight(%-5p)] %c %m%n" />
      <property name="FILE_PATTERN" value="%d{HH:mm:ss.SSS} [%-5p] %c %m%n" />
      <property name="HTML_PATTERN" value="%d{HH:mm:ss.SSS}%-5p%c%m" />
      <property name="CHARSET" value="UTF-8"/>
      <property name="FILE_PATH" value="D:/log"/>
  
      <!--appender-->
      <!--控制台输出-->
      <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
          <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
              <charset>${CHARSET}</charset>
              <pattern>${CONSOLE_PATTERN}</pattern>
          </encoder>
          <!--过滤器：目前只输出error信息-->
          <filter class="ch.qos.logback.classic.filter.LevelFilter">
              <level>ERROR</level>
              <!--DENY, NEUTRAL, ACCEPT;-->
              <!--当匹配到level中给定的类型，则接受(输出)-->
              <onMatch>ACCEPT</onMatch>
              <!--当匹配到level中给定的类型，则不接受(不输出)--> <!--<onMatch>DENY</onMatch>-->
              <!--当匹配到不是level中给定的类型，则不接受(不输出)-->
              <onMismatch>DENY</onMismatch>
              <!--当匹配到不是level中给定的类型，则接受(输出)--><!-- <onMismatch>ACCEPT</onMismatch>-->
          </filter>
      </appender>
      <!--文件输出-->
      <appender name="file" class="ch.qos.logback.core.FileAppender">
          <file>${FILE_PATH}/logback_file.log</file>
          <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
              <charset>${CHARSET}</charset>
              <pattern>${FILE_PATTERN}</pattern>
          </encoder>
      </appender>
  <!--    输出到html-->
      <appender name="htmlFile" class="ch.qos.logback.core.FileAppender">
          <file>${FILE_PATH}/logback_file.html</file>
          <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
              <charset>${CHARSET}</charset>
              <layout class="ch.qos.logback.classic.html.HTMLLayout">
                  <pattern>${HTML_PATTERN}</pattern>
              </layout>
          </encoder>
      </appender>
      <!--基于时间的滚动输出-->
       <appender name="rollingFILe" class="ch.qos.logback.core.rolling.RollingFileAppender" >
           <file>${FILE_PATH}/logback_rollingFILe.log</file>
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <charset>${CHARSET}</charset>
               <pattern>${FILE_PATTERN}</pattern>
           </encoder>
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <!-- 支持压缩:gz格式和zip格式-->
               <fileNamePattern>${FILE_PATH}/logback_rollingFILe_%d{yyyy-MM-dd-HH-mm-ss}.log</fileNamePattern>
               <!--删除超过时间的以前的日志文件 （时间单位取决于fileNamePattern中最小的时间单位）-->
               <maxHistory>20</maxHistory>
               <!--总的日志大小限制（超过了就删除最早的日志-->
               <totalSizeCap>10KB</totalSizeCap>
           </rollingPolicy>
       </appender>
      <!--基于大小和时间的滚动输出-->
      <appender name="rollingFILe2" class="ch.qos.logback.core.rolling.RollingFileAppender" >
          <file>${FILE_PATH}/logback_rollingFILe2.log</file>
          <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
              <charset>${CHARSET}</charset>
              <pattern>${FILE_PATTERN}</pattern>
          </encoder>
          <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
              <!--当前文件超过1MB，就生成新的文件-->
              <maxFileSize>1MB</maxFileSize>
              <!-- 支持压缩:gz格式和zip格式   ; %i：在新增的文件从0递增-->
              <fileNamePattern>${FILE_PATH}/logback_rollingFILe2_%d{yyyy-MM-dd-HH-mm}_%i.log</fileNamePattern>
              <!--删除超过时间的以前的日志文件 （时间单位取决于fileNamePattern中最小的时间单位）-->
  <!--            <maxHistory>20</maxHistory>-->
  <!--            &lt;!&ndash;总的日志大小限制（超过了就删除最早的日志&ndash;&gt;-->
  <!--            <totalSizeCap>10KB</totalSizeCap>-->
          </rollingPolicy>
      </appender>
      <!--开启异步打印日志:一般用于输出文件-->
      <appender name="async" class="ch.qos.logback.classic.AsyncAppender">
          <!--设置阻塞队列的长度，默认为256-->
          <param name="queueSize" value="512"/>
          <!--当阻塞队列的剩余为20&的容量时，会默认丢弃TRACE,DEBUG.INFO级别的日志信息-->
          <!--当discardingThreshold设置为0时，就不会丢弃任何信息-->
          <param name="discardingThreshold" value="0"/>
          <appender-ref ref="file"/>
      </appender>
  
     <!--logger-->
      <!--根输出样式-->
      <root level="INFO">
          <appender-ref ref="async"/>
  <!--        <appender-ref ref="rollingFILe2"/>-->
      </root>
      <!--不同类可以配置不用输出级别和样式-->
      <logger name="com.gyf" level="DEBUG">
          <appender-ref ref="console"/>
      </logger>
  </configuration>
  ```

  ### Log4j2
  
  pom.xml配置文件中添加依赖
  
  ```xml
  <dependencies>
          <dependency>
              <groupId>org.apache.logging.log4j</groupId>
              <artifactId>log4j-core</artifactId>
          </dependency>
          <!--因为SpringBoot默认使用的是logback，如果想要在spring boot中是用log4j2，则需要把logback去除掉-->
          <dependency>
              <groupId>ch.qos.logback</groupId>
              <artifactId>logback-classic</artifactId>
              <exclusions>
                  <exclusion>
                      <groupId>*</groupId>
                      <artifactId>*</artifactId>
                  </exclusion>
              </exclusions>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-logging</artifactId>
              <exclusions>
                  <exclusion>
                      <groupId>*</groupId>
                      <artifactId>*</artifactId>
                  </exclusion>
              </exclusions>
          </dependency>
      </dependencies>
  ```
  
  在类上写的内容
  
  ```java
  import org.apache.logging.log4j.LogManager;
  import org.apache.logging.log4j.Logger;
  
  public class TestLog4j2 {
      public static void main(String[] args) {
          Logger logger = LogManager.getLogger(TestLog4j2.class);
              logger.fatal("致命_FATAL");
              logger.error("错误_ERROR");
              logger.warn("警告_WARN");
              logger.info("信息_INFO");
              logger.debug("调试_DEBUG");
              logger.trace("痕迹_TRACE");
      }
  }
  
  ```
  
  xml文件
  
  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <Configuration>
  <!--<Configuration xmlns="http://logging.apache.org/log4j/2.0/config">-->
      <Properties>
          <Property name="CONSOLE_PATH">%style{%d{HH-mm-ss.SSS}}{yellow} [%-5p] %highlight{%c:} %m%n</Property>
          <Property name="CHARSET">UTF-8</Property>
      </Properties>
      <Appenders>
          <Console name="Console" target="SYSTEM_OUT">
              <PatternLayout pattern="${CONSOLE_PATH}"
                             charset="${CHARSET}"
                             disableAnsi="false"/><!--disableAnsi:是否关闭彩色打印；false为不关闭；true：为关闭-->
              <!--输出大于等于INFO级别的信息-->
  <!--            <ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>-->
              <!--输出小于INFO级别的信息-->
  <!--            <ThresholdFilter level="INFO" onMatch="DENY" onMismatch="ACCEPT"/>-->
              <Filters>
                  <!--NEUTRAL：中立，使用下一个filter进行判断-->
                  <ThresholdFilter level="FATAL" onMatch="DENY" onMismatch="NEUTRAL"/>
                  <ThresholdFilter level="DEBUG" onMatch="ACCEPT" onMismatch="DENY"/>
              </Filters>
          </Console>
          <File name="File" fileName="D:\log\log4j2.log">
              <PatternLayout pattern="%d{HH-mm-ss.SSS} [%-5p] %c: %m%n" charset="${CHARSET}"/>
          </File>
          <RollingFile name="File"
                       fileName="D:\log\log4j2.log"
                       filePattern="D:/log/log4j2/%d{yyyy-MM-dd-HH-mm}_%i.log.gz">
              <PatternLayout pattern="%d{HH-mm-ss.SSS} [%-5p] %c: %m%n" charset="${CHARSET}"/>
              <Policies>
                  <!--按时间间隔生成-->
                  <TimeBasedTriggeringPolicy interval="2" />
                  <!--安大小分-->
                  <SizeBasedTriggeringPolicy maxFileSize="1MB"/>
              </Policies>
              <!--设置滚动输出的%i的大小-->
              <DefaultRolloverStrategy max="10">
                  <!--删除日志文件-->
                  <!--maxDepth：可以访问的层度深度；IfLastModified：删除7秒没有进行修改的文件-->
                  <Delete basePath="D:\log\" maxDepth="10">
                      <IfFileName glob="*1*.log.gz"/>
                      <IfLastModified age="7s"/>
                  </Delete>
              </DefaultRolloverStrategy>
          </RollingFile>
          <!--异步输出-->
          <Async name="async">
              <Appenders-ref ref="RollingFile"/>
          </Async>
      </Appenders>
      <Loggers>
          <Root level="TRACE">
              <AppenderRef ref="Console"/>
              <AppenderRef ref="File"/>
          </Root>
      </Loggers>
  </Configuration>
  ```
  
  