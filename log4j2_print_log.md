 # Java Logging Framework
 
* Good logging requires three things : 

    * The log messages need to provide the required information to understand what the application does internally.
    * Writing log messages has to be as efficient as possible so that it doesn’t affect the performance of your application.
    * You need to be able to adapt the logging details to different deployment environments and situations.

* log4j -> logback -> log4j2 (log4j기반)

* Slf4j
    * It provides a standardized API that is implemented by most of these frameworks.
    * That enables users to change their logging framework without changing their code.
    * Users only need to change the dependency to a different framework that implements the slf4j interfaces. 

* log4j2
    * 가장 최근에 등장했다.
    * logback과 동일하게 자동 리로드 기능과 필터링 기능 

***
# Set Log4j2 with Apache Maven build

* 1) To build with Apache Maven, add the dependencies listed below to pom.xml file.
<!-- Logging -->
		<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-api -->
		<dependency>
    		<groupId>org.slf4j</groupId>
    		<artifactId>slf4j-api</artifactId>
    		<version>1.7.25</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-api -->
		<dependency>
    		<groupId>org.apache.logging.log4j</groupId>
    		<artifactId>log4j-api</artifactId>
    		<version>2.11.1</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-core -->
		<dependency>
    		<groupId>org.apache.logging.log4j</groupId>
    		<artifactId>log4j-core</artifactId>
    		<version>2.11.1</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl -->
		<dependency>
    		<groupId>org.apache.logging.log4j</groupId>
    		<artifactId>log4j-slf4j-impl</artifactId>
    		<version>2.11.1</version>
		</dependency>
    
* 2) Change resources's xml file to 'log4j2.xml'

~~~
<!-- root level = "info" -->
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="warn" >
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
          <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
    <Root level="info">          
        	<AppenderRef ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
~~~

~~~
<!-- root level = "debug" -->
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="warn" >
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
          <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
    <Root level="debug">          
        	<AppenderRef ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
~~~

* Appender : 로그 메시지가 출력될 대상을 결정
  * ConsoleAppender : 콘솔에 로그를 찍는 방법
  * FileAppender : 파일에 로그를 찍는 방법
  * RollingFileAppender : 여러개의 파일을 순회하면서 로그를 찍는 방법
  * SMTPAppender : 메일에 로그를 찍어 보내는 방법
  * DBAppender : 데이터베이스에 로그를 찍는 방법
* Pattern
  * %r : running time
  * %Logger{length} : Logger name을 축약할 수 있음
  * %thread : 현재 thread 이름
  * %-5level : 로그 레벨, -5는 출력의 고정폭 값
  * %msg(%message/%m) : 로그메시지
  * %n : new line
  * %highlight : 로그레벨에 따른 색을 줌 


* 3) Test in java file

~~~
package dk.gaia.web;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LoggingTest
{
	Logger logger = LoggerFactory.getLogger(LoggingTest.class);

	public void testMethod()
	{
		logger.info("This is testing of log4j2!!");
		logger.debug("This is testing of appender !!");
	}
	public static void main(String[] args)
	{
		new LoggingTest().testMethod();
	}
}
~~~

  * root level = "info"
  
  ![image](https://user-images.githubusercontent.com/32332719/55716083-10026300-5a31-11e9-9f07-a5446ffc40cf.png)
  
  * root level = "debug"
  
  ![image](https://user-images.githubusercontent.com/32332719/55716096-15f84400-5a31-11e9-8d2c-e977addbb236.png)
  
  ###
  # Conclusion
  * logback vs log4j2
    * Apache Log4j2
      * It is the fastest and most advanced of the three frameworks.
      * It allows lazy evaluation of log statements based on lambda expressions.(자바8이 람다식에서 로그메시지를 래핑하도록 하는 API제공)
      * It offers asynchronous loggers for low-latency systems.(비동기: 응답을 기다려야 하는 일이 있을 때 기다리지 않고 바로 다른 작업)
      * It provides a garbage-free mode to avoid any latency caused by garbage collector operations.

