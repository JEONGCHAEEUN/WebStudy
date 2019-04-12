# LevelDB
  * 구글에 의해 개발된 levelDb는 빠르고, 가볍다.
  * DB자체를 설치하지 않고 DB를 그 자리에서 만들어 쓴다.
  * 따라서 DB를 만드는 소스를 각각의 언어에 맞게 선택해야한다.
  * key와 value로 저장을 하는 NoSQL이라는 특징이 있다.
  * 데이터는 key로 정렬되어 저장된다.
  * 대용량 서버용으로 개발되기 보다는 단말 혹은 브라우저의 storage library용도로 개발되는 Embedded Database이다.
  * 기본 operation들로는 put(key, value), get(key), delete(key)들이 있다.
  * 한번의 atomic batch에서 여러 변경을 할 수 있다.
  * 사용자들은 일시적인 스냅샷을 만들어서 일관된 데이터 보기를 얻을 수 있다.
  * 전반적으로 뛰어난 성능을 보이지만, 큰 데이터를 쓰는 부분에선 오히려 성능이 저하된다.
  
# Use levelDB in JAVA, STS

  * dependency 설정
  
    * Maven project에 다음과 같은 dependency들을 설정해준다.
    
    ~~~
    <dependency>
        <groupId>org.iq80.leveldb</groupId>
        <artifactId>leveldb-api</artifactId>
        <version>0.9</version>
    </dependency>

    <dependency>
        <groupId>org.iq80.leveldb</groupId>
        <artifactId>leveldb</artifactId>
        <version>0.9</version>
    </dependency>
    ~~~
    
   * java파일을 생성해서 levelDB Store을 초기화 해준다.
   
   ~~~
   import org.iq80.leveldb.DB;
   import static org.iq80.leveldb.impl.Iq80DBFactory.factory;
   import org.iq80.leveldb.Options;
   
   DB levelDBStore;
   Options options = new Options();
   levelDBStore = factory.open(new File("levelDBStore"),options);
   ~~~
    * project home 안에 levelDBStore이라는 디렉토리 생성
    
# Basic Operations
  
  * basic operation은 put, get, delete 세 개로, 다른 데이터베이스들에 비해 다루기 쉽다는 것을 알 수 있다.
  
  * Put Operation
    
    * 주어진 key 아래 주어진 value를 저장한다.
    * data는 byte arrays(byte[])로 저장된다.
    * 따라서 문자열의 value는 (String st).getBytes()를 써서 byte arrays로 변환해줘야한다.
    
    ~~~
    levelDBStore.put("key".getBytes(), "value".getBytes());
    ~~~
    
  * Get Operation
  
    * get operation은 주어진 key 아래에 저장된 value를 반환한다.
    * value는 key를 통해서만 접근될 수 있다.
    * LevelDB에는 value에 상응하는 key를 얻을 수 있는 메카니즘이 없다.
    
    ~~~
    levelDBStore.get("key".getBytes());
    ~~~

  * Delete Operation
  
    * 주어진 key와 같이 저장된 value를 지우는 operation이다.
    
    ~~~
    levelDBStore.delete("key".getBytes());
    ~~~
   
  - - -
  * 이것들이 levelDB의 모든 basic operation이다. 
  * 데이터베이스로의 모든 변형은 get이 values를 검색하는 동안, put과 delete 를 통해서 이뤄진다.
 
# Advanced Functionalities
  
  * levelDB에는 다양한 상황에서 실용적인 기능들이 더 있다.
  * write batches, snapshots and iterators 에 대해 알아볼 것이다.
  
  * Write Batch
  
    * Write Batches는 database의 atomic 업데이트를 수행핼 때 사용된다.
    * atomic은 업데이트가 완벽하게 수행되거나 업데이트가 하나도 수행되지 않을 때를 뜻한다.
    * 즉, 부분적인 수행은 없다는 뜻이다.
    * Write Batch의 수행을 위해서, class를 import하고, 초기화를 해줘야한다.
    
    ~~~
    import org.iq80.leveldb.WriteBatch;
    
    WriteBatch batch = levelDBStore.createWriteBatch();
    ~~~
    
    * atomically하게 수행돼야 할 operation들을 batch에 넣어줄 수 있다.

    ~~~
    batch.put("key1".getBytes(), "value1".getBytes());
		batch.put("key2".getBytes(), "value2".getBytes());
		batch.delete("key3".getBytes());
    ~~~
    
    * 그 후 밑의 라인을 통해서 batch가 수행될 수 있다.
    
    ~~~
    levelDBStore.write(batch);
    ~~~
    
    * batch에 삽입된 operation들은 database로 모두 써지거나 아예 안써진다는 levelDB의 보장이 있다.
    * 즉, 위의 batch에서 key1과 key2는 업데이트 되고 key3은 삭제되지 않을 일은 없다.
    
    * 메모리의 누수를 피하기 위해 batch를 사용한 이후 닫는 것을 잊지말아야 한다.
    
    ~~~
    batch.close();
    ~~~
    
    * Write batch는 내장된 기능으로 사용할 수 없는 levelDB에서 트랜잭션을 수행하기 위해 사용될 수 있다.

  * Snapshot
  
    * snapshot은 주어진 시점에서 database의 상태를 저장할 수 있다.
    * 읽기 목적이 있을때만 사용된다.
    
    * 아래와 같이 class를 import 해주고 초기화 시킨다.
    
    ~~~
    import org.iq80.leveldb.Snapshot;
    Snapshot snapshot = levelDBStore.getSnapshot();
    ~~~
    
    ~~~
    levelDBStore.put("a".getBytes(),"1".getBytes());
		Snapshot snapshot = levelDBStore.getSnapshot();
		levelDBStore.put("a".getBytes(),"2".getBytes());
		levelDBStore.get("a".getBytes(), new ReadOptions().snapshot(snapshot)); // returns 1
		levelDBStore.get("a".getBytes()); // returns 2
    ~~~
    
      * key "a"의 값이 "1"에서 "2로 업데이트 됐다. 
      * 그러나 1에서 2로 바뀌는 라인 사이에 database의 snapshot이 진행됐다.
      * 그 단계에서 key "a"의 value는 "1"이었고, snapshot에 저장됐다.
      * 그러므로, snapshot으로부터 "a"를 읽을 때 value로 "1"이 반환됐다.
      * database로부터 읽을 때는 value로 "2"가 반환됐다.
    
    * 그러나 LevelDB에는 snapshot에 쓰거나 snapshot으로 database를 snapshot으로 되돌릴 메카니즘이 없다.
    * key가 database에 써졌을 때, database의 상태가 되돌려질 수는 없다.
    
  * Iterator
  
    * Iterator는 저장된 key들을 가로지르곤 한다.
    * LevelDB에서는 key들이 사전순으로 저장된다.
    * Iterators는 key들을 통해 앞뒤로 이동하고, 원하는 operation들을 수행하는데 사용할 수 있다.
    
    * 역시, 클래스를 import하고, 초기화를 해준다.
    
    ~~~
    import org.iq80.leveldb.DBIterator;
    DBIterator iterator = levelDBStore.iterator();
    ~~~
    
    * iterator에 시작점을 제공하면, iterator는 특정 key로부터 시작하도록 구성할 수 있다.
    * 그렇지 않으면 사전순으로 정렬된 key들의 처음 혹은 마지막에서 시작하도록 구성할 수 있다.
    
    ~~~
    iterator.seek("keys".getBytes()); // starts from the specified key
		iterator.seekToFirst(); // starts from the first key
		iterator.seekToLast(); // starts from the last key
    ~~~
    
    * 그러면 그 iterator는 아래의 코드처럼 while문을 이용해 앞뒤로 이동할 수 있다.
    ~~~
    while (iterator.hasNext()){
      byte[] key = iterator.peekNext().getKey();
      byte[] value = iterator.peekNext().getValue();
      // whatever you want to do
      iterator.next();
    }
    ~~~
    
    * 마지막 key에 도착하면 while문을 빠져나간다.
    * 아니면 요구사항에 따라 while문에 break조건을 작성할 수도 있다.
    
    * 마지막으로, 메모리 누수 방지를 위해 iterator를 닫아준다.
    
    ~~~
    iterator.close();
    ~~~
    
    
    * 항상! 프로그램이 완료된 후에 database를 닫아줘야한다.
    
    ~~~
    levelDBStore.close();
    ~~~
    
    
    
    
    ~~~
