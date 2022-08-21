## log4j

### 架构图

![classes](https://logging.apache.org/log4j/2.x/images/Log4jClasses.jpg)

- LoggerContext：Logging System的锚点（我的理解是入口）

- Configuration：每一个 LoggerContext 都有一个有效的 Configuration， Configuration 包含所有的Appender 、Filter、LoggerConfig ，StrSubstitutor引用和对Layout的格式设置

- Logger：继承自`AbstractLogger`, 通过名字与LoggerConfig互相协作

  当配置被修改后，它将与不同的 LoggerConfig 相关联，这导致其行为也被改变。

  - 如何获得一个Logger实例

    ```java
    LogManager.getLogger("name");
    ```

    

- LoggerConfig：在`Logger`被声明时创建，

  它包含了一组一组用于过滤传递给Appender事件的Filter，以及用于处理事件的Appender引用

- Filter：用来过滤消息事件，过滤的结果为以下三种之一

  - `Acccept`：接收，不需要经过后续的Filter
  - `Deny`：拒绝
  - `Neutral`：将事件继续提交给下一个Filter

- Appender：日志的输出目标（文件，控制台，服务器etc）

  - 一个Logger可以有多个Appender

  - Appender的积累：不光会使用当前`LoggerConfig`中定义的Appender，也会使用它的先祖`LoggerConfig`的Appender

    除非在配置文件中设置`additivity="false"`

- Layout：日志的输出格式

### 配置log4j

1. 在`build.gradle`中添加

   ```groovy
   configurations {
       all*.exclude module : 'spring-boot-starter-logging'
   }
   
   dependencies {
       implementation 'org.springframework.boot:spring-boot-starter-webflux'
   
       implementation 'org.apache.logging.log4j:log4j-api:2.18.0'
       implementation 'org.apache.logging.log4j:log4j-core:2.18.0'
   
       implementation group: 'com.fasterxml.jackson.core', name: 'jackson-databind', version: '2.13.3'
       implementation group: 'com.fasterxml.jackson.dataformat', name: 'jackson-dataformat-yaml', version: '2.13.3'
   
       testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
       testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'
   }
   ```

   - `spring-boot`默认使用`Logback `, 所以要把相关的模块移除

   - 为了使用`yml`格式的配置文件，需要添加下面两个dependency

     - com.fasterxml.jackson.core:jackson-databind
     - com.fasterxml.jackson.dataformat:jackson-dataformat-yaml

   - 此时因为还没有添加配置文件，若运行使用log4j2的程序会生成下面默认的配置

     - A [ConsoleAppender](https://logging.apache.org/log4j/2.x/log4j-core/apidocs/org/apache/logging/log4j/core/appender/ConsoleAppender.html) attached to the root logger.

     - A [PatternLayout](https://logging.apache.org/log4j/2.x/log4j-core/apidocs/org/apache/logging/log4j/core/layout/PatternLayout.html) set to the pattern "%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" attached to the ConsoleAppender

2. 新建配置文件`log4j2.yml`, 并在`application.yml`中添加`logging.config`属性，值为`log4j2.yml`的所在路径

3. file rotation设置

   - 需求，每天0点将日志文件打包成application-yyyymmdd.log.gz

   - 需要用到`RollingFileAppender`, 它会根据`TriggeringPolicy `, `RolloverPolicy`对日志文件进行rotation
   
   - `TriggeringPolicy`什么时候触发rotation行为
     - 这里使用`Cron Triggering Policy`: 根据时间触发rotation
     
   - `RolloverPolicy`如何对日志进行rotation
     - 文件名：在`RollingFileAppender`的`filePattern `属性定义
     
     ```yaml
     configuration:
       status: debug
       appenders:
         console:
           name: STDOUT
           target: SYSTEM_OUT
           patternLayout:
             pattern: "[%d] [%-5p] [%t] %m%n"
         rollingFile:
           name: FILE
           fileName: "./logs/application.log"
           filePattern: "./logs/application-%d{yyyy-MM-dd}.log.gz"
           patternLayout:
             pattern: "[%d] [%-5p] [%t] %m%n"
           policies:
             CronTriggeringPolicy:
               schedule: "0 0 0 * * ?"
     
       loggers:
         logger:
           name: log.kinki.land
           level: debug
           additivity: true
           appenderRef:
             ref: FILE
         root:
           level: debug
           appenderRef:
             ref: STDOUT
     ```
     
     