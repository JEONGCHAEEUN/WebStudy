# LevelDB 
 * LevelDB is a fast and lightweight key/value DB library.
 * key/value DB means NoSQL.
 * lightweight library means Embedded DB.
 * So, LevelDB is NoSQL Embedded DB.
 
 # 특징
 * Keys and values are arbitrary byte arrays.
 * Data is stored sorted by key.
 * The basic operations are Put(key, value), Get(key), Delete(key)
 * Multiple changes can be made in one atomic batch.
 * Users can create a transient snapshot to get a consistent view of data.
    * options.snapshot = db->GetSnapshot();
 * Forward and backward iteration is supported over the data.
 * 전체적으로 성능이 뛰어나지만 value사이즈가 크면 성능이 저하된다.
