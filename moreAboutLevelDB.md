# Database
* Relation DB
    * mysql, mariaDB
* State DB 
    * NOsql(Not-Only sql)로, 질의가 없고 key-value로 data를 저장한다.
    * 최초 테이블 생성시 데이터간의 관계를 정의하지 않아 빠른 속도의 설계가 가능하다.
    * levelDB, couchDB

# LevelDB
* levelDB에서는 key와 value로 저장을 하고, data는 key로 정렬된다.
* data는 byte array로만 저장이 되기 때문에 byte array형태로 꼭 변환을 시켜줘야 한다.
* 데이터 처리 기능인 CRUD로 보면 다음과 같다.
    * C(CREATE) : Put operation을 통해서 생성한다.
        * levelDBStore.put("key".getBytes(), "value".getBytes());
    * R(RETRIEVE) : get operation을 통해서 키값으로 value값을 가져올 수 있다.
        * levelDBStore.get("key".getBytes());
    * U(UPDATE) : put으로 또다른 key-value를 생성하여 업데이트하거나 기존의 key값에 대한 value를 바꿀 수 있다.
        * levelDBStore.get("key2".getBytes(), "value2".getBytes());
    * D(DELETE) : delete operation으로 기존 key값과 key값에 저장된 value값까지 삭제한다.
        * levelDBStore.delete("key".getBytes());

### 그러나 하나의 key값에 여러 value들을 이 방법만으로는 저장할 수 없었다!!
### ->JSON으로 data관리!
JSON.md

# levelDB의 데이터를 JSON으로 관리

* JSON에서 필요한 class들 import 해주기
~~~
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.json.simple.parser.ParseException;
~~~

* main함수에 levelDBStore 초기화 해주는 것은 같다.
~~~
DB levelDBStore;
Options options = new Options();
levelDBStore = factory.open(new File("levelDBStore"), options);
~~~

* data라는 이름의 JSONObject 클래스를 선언해서 data를 생성한다.
~~~
JSONObject data = new JSONObject();
data.put("name", "lee");
data.put("age", 28);
data.put("shape", "hand");
~~~

* levelDBStore에 CRUD할 때는 byte array로 바꿔줘야 하므로 byte[] 형의 dt라는 이름을 선언해 줘서 JSONObject -> toString() -> getBytes() 순서로 자료형을 바꿔준다.
~~~
byte[] dt = data.toString().getBytes();
levelDBStore.put("yo".getBytes(), dt);
System.out.println(data.toString());
~~~
![image](https://user-images.githubusercontent.com/32332719/56120819-e81f7c00-5fa9-11e9-8332-5de14c0da60f.png)


* JSON의 데이터를 handling하기 위해서는 bytes[]형태를 다시 JSON의 형태로 바꿔줘야한다.
* 따라서, 위에서와는 반대로 bytes[]를 String으로 바꾸고 다시 JSON object로 바꿔줘야 json object의 data 각각에 접근을 해서 다룰 수가 있다.
* 우선 새로운 String 객체를 선언해서 levelDBStore의 "yo"라는 키의 값들을 읽어온다.
* 그럼 "yo"라는 키의 value값들이 현재 문자열로 존재하게 된다.
* String에서 JSON의 형태로 가져오려면 JSONParser 객체가 필요하다.
* JSONObject를 선언해서 String 객체를 parse해서 JSONObject 형 객체에 저장한다.
* 그러면 jsonObject의 각각의 키들에 접근을 할 수 있다.
~~~
String rr = new String(levelDBStore.get("yo".getBytes()));		
JSONParser jsonParser = new JSONParser();
try {
	JSONObject jsonObject = (JSONObject)jsonParser.parse(rr);
	System.out.println(jsonObject.get("name"));
} catch (ParseException e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
}
~~~

* 한 key에 여러개의 value를 array로 넣어서 handling 하기
    * ![image](https://user-images.githubusercontent.com/32332719/56140026-051d7480-5fd5-11e9-963f-2a4e78c1f9ca.png)
    * 위와 같은 형태의 Object를 다룰 것이기 때문에
    * 우선 "singer"라는 하나의 JSONObject를 선언해주고,
    * "singer"의 value로 들어가는 array를 각각 JSONObject a와 b로 선언해서 put으로 생성해준다.
    * a와 b 두 개의 JSONObject를 넣어줄 JSONArray인 "singerArray"를 생성한다.
    * singer라는 이름의 JSONObject에 key와 value값을 넣어주는데,
    * key에는 "singer"라는 이름으로, value에는 array형태인 singerArray를 넣는다. 
    * 그러고 출력해보면 위 사진과 같은 JSONObject가 나온다.
~~~
JSONObject singer = new JSONObject();
JSONObject a = new JSONObject();
a.put("name", "anne");
a.put("age", 29);
JSONObject b = new JSONObject();
b.put("name", "troye");
b.put("age", 27);
JSONArray singerArray = new JSONArray();
singerArray.add(a);
singerArray.add(b);
singer.put("singer", singerArray);
System.out.println(singer.toString());
~~~

* singerArray부분의 각각의 key에 대한 value를 다루기 위해 
* 새로운 JSONArray인 parsingArray를 선언해서 
* singer Object의 "singer"에 대한 value값, 즉 singerArray부분을 저장한다.
* JSONArray의 각각의 Object를 JSONObject인 returnName이라는 이름으로 저장을 하고,
* 그 Object의 키값을 get함수로 뽑아오면 value값을 얻을 수 있다.
~~~
try {
	JSONArray parsingArray = (JSONArray)singer.get("singer");
	for(int i = 0; i<parsingArray.size(); i++) {
		JSONObject returnName = (JSONObject)parsingArray.get(i);
		System.out.println(returnName.get("name"));
				
	}
}catch(Exception e) {
	e.printStackTrace();
}
~~~
* 출력 결과는 다음과 같다.

![image](https://user-images.githubusercontent.com/32332719/56141159-3b5bf380-5fd7-11e9-903d-da1ab2f854ac.png)


~~~
JSONObject singer = new JSONObject();
JSONObject a = new JSONObject();
a.put("name", "anne");
a.put("age", 29);
byte[] bytea = a.toString().getBytes();
levelDBStore.put("a".getBytes(), bytea);
String stringa = new String(levelDBStore.get("a".getBytes()));
		
JSONObject b = new JSONObject();
b.put("name", "troye");
b.put("age", 27);
byte[] byteb = b.toString().getBytes();
levelDBStore.put("b".getBytes(), byteb);
String stringb = new String(levelDBStore.get("b".getBytes()));
		
		
JSONArray singerArray = new JSONArray();
singerArray.add(a);
singerArray.add(b);
singer.put("singer", singerArray);
byte[] bytesinger = singer.toString().getBytes();
levelDBStore.put("singer".getBytes(), bytesinger);
String stringsinger = new String(levelDBStore.get("singer".getBytes()));
System.out.println(stringsinger + "qww");
		
		
try {
	JSONArray parsingArray = (JSONArray)singer.get("singer");
	for(int i = 0; i<parsingArray.size(); i++) {
		JSONObject returnName = (JSONObject)parsingArray.get(i);
		System.out.println(returnName.ge("name"));
		
	}
}catch(Exception e) {
	e.printStackTrace();
}
~~~