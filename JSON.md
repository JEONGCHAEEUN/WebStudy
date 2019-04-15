# JSON(JavaScript Object Notation)
  * JavaScript 객체 문법을 따르는 문자 기반의 데이터 형식이다.
  * 문자열 형태로 존재하기 때문에 네트워크로 전송할 때 유용하다.
  * 경량의 data 교환 형식
  * Javascript의 기본 데이터 타입인 String, array, boolean, 숫자, 그리고 다른 객체를 포함할 수 있다.
  * 순수하게 데이터 형식이기 때문에 메서드는 담을 수 없고, 프로퍼티만 담을 수 있다.
  * 문자열과 프로퍼티의 이름을 작성할 때, 큰따옴표만 사용할 수 있다.
  
  * object 는 name/value 쌍들의 비순서화된 SET
    * { String1 : value1, String2 : value2 ...}
    
    ![스크린샷 2019-04-15 오전 8 06 05](https://user-images.githubusercontent.com/32332719/56100578-79183800-5f55-11e9-9cfb-9013b20d1b74.png)

  * array는 값들의 순서화된 collection이다.
    * [ value1, value2, ...}
    
    ![스크린샷 2019-04-15 오전 8 06 15](https://user-images.githubusercontent.com/32332719/56100579-7b7a9200-5f55-11e9-97f8-ca96390e1172.png)

  * value는 큰따옴표 안에 string, number, boolean, null, array, object 가 올 수 있다.
  
# JSON 함수
  * JSON.parse()
    * Json형태의 String을 객체형 object로 변경하는 메소드이다.
    * jsonStr은 작은따옴표로 묶인 String 형태이다.
    ![스크린샷 2019-04-15 오전 8 12 51](https://user-images.githubusercontent.com/32332719/56100647-66523300-5f56-11e9-8091-4541f8c11e90.png)
      
    * json객체로 변경한 jsonStr에서 .key를 사용해 value값을 얻을 수 있다.
    ![스크린샷 2019-04-15 오전 8 13 07](https://user-images.githubusercontent.com/32332719/56100649-68b48d00-5f56-11e9-93b7-318ac9408250.png)

    * value값에 JSON객체 타입이 다시 들어가있는 이중,삼중구조도 가능하다.
    ![스크린샷 2019-04-15 오전 8 21 34](https://user-images.githubusercontent.com/32332719/56100752-833b3600-5f57-11e9-8297-a77a4d0daec2.png)

    * jsonObj의 key값의 value전체를 뽑아내보기
    ![image](https://user-images.githubusercontent.com/32332719/56101275-217dca80-5f5d-11e9-9ab1-58d1ba4f45ff.png)

    ![image](https://user-images.githubusercontent.com/32332719/56101303-699ced00-5f5d-11e9-81c1-bf46736d67f8.png)
      
   * JSON.stringify()
     * JSON Object 형태를 String타입으로 변경하는 메서드이다.
      
     * json 객체 타입 jsonObj를 선언하고, stringify()를 써서 문자열도 출력해보면 다음과 같다.
     ![image](https://user-images.githubusercontent.com/32332719/56101476-0dd36380-5f5f-11e9-802f-097c9e90a02e.png)
     ![image](https://user-images.githubusercontent.com/32332719/56101478-10ce5400-5f5f-11e9-913b-565133d6e009.png)
      
      
