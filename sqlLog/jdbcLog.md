# 输出不带“？”的完整sql语句的配置
1. pom.xml 中添加依赖包
```
	<dependency>
		<groupId>com.googlecode.log4jdbc</groupId>
	    <artifactId>log4jdbc</artifactId>
	    <version>1.2</version>
	</dependency>
```
2. 修改数据源配置（以spring-boot application.properties 为例）
```
# 修改前
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/db_test?autoReconnect=true&characterEncoding=utf-8
# 修改后
spring.datasource.driver-class-name=net.sf.log4jdbc.DriverSpy
spring.datasource.url=jdbc:log4jdbc:mysql://localhost:3306/db_test?autoReconnect=true&characterEncoding=utf-8
```
3. 配置 logback.xml
```
	<?xml version="1.0" encoding="UTF-8"?>
	<configuration debug="false">
	
		<!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
	    <property name="LOG_HOME" value="/web/logs" />
	    
	    <!-- 控制台输出 -->
	    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
	        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
	            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
	            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
	        </encoder>
	    </appender>
	    
	    <appender name="FILE"  class="ch.qos.logback.core.rolling.RollingFileAppender">
	        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
	            <!--日志文件输出的文件名-->
	            <FileNamePattern>${LOG_HOME}/app-%d{yyyy-MM-dd}.log</FileNamePattern>
	            <!--日志文件保留天数-->
	            <MaxHistory>30</MaxHistory>
	        </rollingPolicy>
	        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
	            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
	            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
	        </encoder>
	        <!--日志文件最大的大小-->
	        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
	            <MaxFileSize>10MB</MaxFileSize>
	        </triggeringPolicy>
	    </appender>
	    <!--自定义 appender-->
		<appender name="mine" class="com.osa.platform.filter.MyAppender"></appender>
	    
	    <logger name="jdbc.connection" level="OFF"/>
	    <logger name="jdbc.resultset" level="OFF"/>
	    <logger name="jdbc.resultsettable" level="INFO"/>
	    <logger name="jdbc.audit" level="OFF"/>
	    <logger name="jdbc.sqltiming" level="INFO"/>
	    <logger name="jdbc.sqlonly" level="OFF"/>
	    
	 <!-- 日志输出级别 -->
	    <root level="INFO">
	        <appender-ref ref="STDOUT" />
	        <appender-ref ref="FILE" />
	        <appender-ref ref="mine" />
	    </root>
	    
	</configuration>
```
