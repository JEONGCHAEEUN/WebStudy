# java.util.Properties

  * java.util.Properties 클래스를 이용한 어플리케이션의 프로퍼티 설정
  * key와 value를 사용한다.
  
  ## 설정시스템을 왜?
  
   * 웹서버 개발시 몇 번 포트를 사용할지, 루트는 어느 디렉토리를 사용할지, 기본 문서를 index.html로 할지, index.htm으로 할지 등등
   * 직접 프로그램 코드에 삽입하면 쉽긴 하지만
   * 설정정보를 바꾸려면 소스를 재컴파일 해야하고, 
   * 개발시와 배포시 사용되는 환경이 달라서 배포시 소스코드를 재컴파일 해야한다.
   * 애플리케이션을 실제 사용하는 도중 설정값을 바꾸려면 개발자가 일일이 변경해줘야한다.
    
   * =>따라서 설정파일을 작성해야한다.
   * java.util.Properties 클래스를 사용해 설정파일로부터 설정정보를 읽어오는 클래스인 Configuration클래스를 작성한다.
    
  ## Properties 클래스 
  
   * 프로퍼티의 지속적인 집합을 나타낸다
   * 프로퍼티의 목록을 스트림으로 저장하거나 스트림으로부터 읽어올 수 있다.
   * java.util.Hashtable클래스를 상속하고 있어서 프로퍼티는 <키, 값>의 형태로 저장된다.
   * Hashtable의 put(), setProperty()로 키,값을 추가하거나 변경하는 것이 쉽다.
   * getProperty()는 키, 값을 추출함
     * public String getProperty(String key)
       * key를 키 값으로 갖는 프로퍼티가 있으면 그 값을 리턴, 없으면 null
     * public String getProperty(String key, String defaultValue)
       * key를 키 값으로 갖는 프로퍼티가 있으면 그 값을 리턴, 없으면 null을 리턴
        
    
  ## 설정 파일의 형태
  
   * Configuration 클래스가 인식할 수 있는 설정파일의 형태
   
   -------------
   #주석
   key1 = value1
   key2 = value2
   ...
   -------------
    
   * 웹서버를 위한 설정파일
   ___________________________________________________
   #웹서버가 사용할 포트번호
   webserver.port = 8080
   #문서의 루트 디렉토리
   webserver.root_directory = /home/madvirus/webrot/
   #기본적으로 읽어올 문서
   webserver.default_document = index.html, index.htm
   ___________________________________________________
    
   (웹 서버 말고 다른곳에서도 DB커넥션 풀에서 사용할 JDBC드라이버의 종류, 최대로 열 수 있는 Connection의 개수 등을 지정가능)
    
   * Configuration클래스는 String, int, double, boolean 같은 타입을 처리할 수 있어야함.
    
   # Config Interface, AbstractConfiguration Class
    
   ![image](https://user-images.githubusercontent.com/32332719/55857981-79a57d00-5ba9-11e9-822e-565d952129cf.png)

   * Configuration 클래스는 단지 로컬에 있는 파일로부터 프로퍼티를 읽어온다.
   * RMIConfiguration, HTTPConfiguration 같은 별도의 클래스에서 원격으로부터 프로퍼티를 읽어온다.
      
      
  ## Configuration 클래스의 문제점
  
   * Configuration 클래스의 인스턴스를 생성할 때 마다 매번 설정파일을 읽어온다.
   * 여러 파일로부터 설정정보를 읽어올 경우 서로 다른 파일에 있는 프로퍼티의 키 값이 겹치지 않도록 해야한다.
   * 설정파일만으로는 각 프로퍼티간의 관계를 알 수 없어서 프로퍼티간의 관계를 명시적으로 표시할 수 있으면 설정파일을 관리하는게 수월해질 것이다.
   * 프로퍼티들의 집합 이라는 개념이 존재하지 않는다.
    
   * 이 문제점 중 몇가지는 XML로 해결 가능하다.
    
  ## STS tool에서 프로퍼티  
  
   * src/main/java 에 com.edu.exam01이라는 패키지를 하나 생성했다.
   * 그 패키지에 AdminConnection.java 파일과 Main.java 파일을 각각 생성했다.
   * src/main/resources 에 admin.properties 파일과 applicationCTX.xml파일을 생성했다.
   * properties 파일은 classpath를 통해 이용하기 위해 resources 밑에 생성을 했다.
    
   ### admin.properties
    
   ~~~
   admin.id = jeong 
   admin.pwd = 12341234
   ~~~
    
   * 간단하게 admin의 id에 jeong을 저장하고, admin의 pwd에 12341234를 저장했다.
    
   ### applicationCTX.xml
    
   ~~~
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-           beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">
   <context:property-placeholder location="classpath:admin.properties"/>
    
   <bean id="adminConnection" class="com.edu.exam01.AdminConnection">
       <property name="adminId">
           <value>${admin.id}</value>
       </property>
       <property name="adminPwd">
           <value>${admin.pwd}</value>
       </property>
   </bean>
   </beans>
   ~~~
    
   ### AdminConnection.java
    
   ~~~
   package com.edu.exam01;
 
   public class AdminConnection {

       private String adminId;
       private String adminPwd;

       // constructor
       public AdminConnection() {

       }

       public AdminConnection(String adminId, String adminPwd) {
           this.adminId = adminId;
           this.adminPwd = adminPwd;
       }
        // get set method
       public String getAdminId() {
           return adminId;
       }
        public void setAdminId(String adminId) {
           this.adminId = adminId;
       }
        public String getAdminPwd() {
           return adminPwd;
       }
        public void setAdminPwd(String adminPwd) {
           this.adminPwd = adminPwd;
       }
        @Override
       public String toString() {
           return "Property check// [adminId=" + adminId + ", adminPwd=" + adminPwd + "]";
       }
   }
   ~~~
    
   * xml파일에서 <beans>에 property-placeholder를 선언해서 ${}로 property place holder를 만들고, 프로퍼티 파일을 읽어서 value를 바꿔치기함
   * ${} 방식의 단점으로는 프로퍼티 파일의 키 값에 ${prefix}, suffix를 붙여서 find-and-replace방식으로 동작한다.
   * - 키값에 오타가 있거나 제대로 안넣었으면 ${}가 그대로 남는다.
    
    
   * admin.properties파일을 get, set 해준다.
    
   ### Main.java
    
    ~~~
    package com.edu.exam01;

    import org.springframework.context.support.GenericXmlApplicationContext;

    public class Main {

        public static void main(String[] args) {

            GenericXmlApplicationContext gxac = new GenericXmlApplicationContext();
            gxac.load("classpath:applicationCTX.xml");
            gxac.refresh();

            AdminConnection adminConnection = gxac.getBean("adminConnection", AdminConnection.class);
            System.out.println(adminConnection.toString());
            gxac.close();
        }
    }
    ~~~
    
   ![image](https://user-images.githubusercontent.com/32332719/55941552-6368f100-5c7d-11e9-9a40-193bfd54fc1d.png)

     * GenericXmlApplicationContext클래스 : XML로부터 객체설정 정보를 읽어와서 객체생성과 초기화를 수행한다.
     * BeanFactory 인터페이스의 먼 자손이다.
     * getBean() : 생성된 객체를 검색하는 함수(BeanFactory에 정의돼있음)

   #### 근데 이렇게 파일을 4개씩이나 안만들고 바로 properties파일에서 Main으로 읽어올 수 있다고 하는데 못해봤다...

 # Conclusion
  
 * java.util.Properties 클래스를 이용해서 설정파일로부터 프로퍼티를 쉽게 읽어오는 법을 배웠다.
 * Configuration 클래스는 CLASSPATH에 있는 자원으로부터 프로퍼티를 읽어 매우 손쉽게 어플리케이션의 여러 프로퍼티를 설정할 수 있도록 해준다.
    
