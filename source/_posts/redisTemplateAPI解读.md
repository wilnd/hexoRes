---
title: redisTemplateAPI解读
date: 2018-2-14 11:00:50
tags: java redis
categories: java
---
腾讯云开发手册 [link>>](https://cloud.tencent.com/developer/chapter/16273)
1. afterPropertiesSet - 初始化方法

2. boundGeoOps - 地理位置相关
```
boundGeoOps = redisTemplate.boundGeoOps(RedisKeyConstant.USER_GEO_KEY);
1. 为成员增加某个地理位置的坐标
boundGeoOps.geoAdd(new Point(lng, lat), Id);
2. 获取两个成员之间的地理位置的距离,可以设置距离单位
Distance geoDist = boundGeoOps.geoDist(fromMember, toMember);
double value = geoDist.getValue();
3. 获取某个成员的地理位置的坐标
List<Point> geoPos = boundGeoOps.geoPos(Member);
4. 根据给定地理位置坐标获取指定范围内的地理位置集合
 Circle circle = new Circle(new Point(lng, lat), new Distance(range,Metrics.KILOMETERS));
//可以指定分页数量,以及排序规则
GeoResults<GeoLocation<Object>> geoRadius = boundGeoOps.geoRadius(circle,GeoRadiusCommandArgs.newGeoRadiusArgs().sortDescending().limit(count));
5. 获取某个地理位置的hash值
List<String> hashLists = boundGeoOps.geoHash(Member);
6.根据给定成员获取指定范围内的地理位置集合
 GeoResults<GeoLocation<Object>> geoResults =boundGeoOps.geoRadiusByMember(member, new Distance(range, Metrics.KILOMETERS), GeoRadiusCommandArgs.newGeoRadiusArgs().sortDescending().limit(count));
```
3. boundHashOps - hash操作方法
- Redis 中每个 hash 可以存储 2^32 - 1 键值对（40多亿）。
- hash 是一个string类型的field和value的映射表
```


1. 定义一个BoundHashOperations
        BoundHashOperations<String, String, Object> boundHashOperations = redisTemplate.boundHashOps("li");
2. put(HK key, HV value) 新增元素到指定键中
        boundHashOperations.put("h1","w1");
3. getKey() 获取指定键中的值
        String key = boundHashOperations.getKey();
4. values() 获取map中的值
        List<Object> values = boundHashOperations.values();
5. entries() 获取map中的键值对
        Map<String, Object> entries = boundHashOperations.entries();
6. get(Object member) 获取map键中的值
        Object w1 = boundHashOperations.get("h1");
7. keys() 获取map的键
        Set<String> keys = boundHashOperations.keys();
8. multiGet(Collection<HK> keys)  根据map键批量获取map值
        List<Object> objects = boundHashOperations.multiGet(new ArrayList<>());
9. putAll(Map<? extends HK,? extends HV> m) 批量添加键值对
        boundHashOperations.putAll(new HashMap<>());
10. increment(HK key, long delta)
        boundHashOperations.increment("c",1); //键c对应的值会+1
11. putIfAbsent(HK key, HV value) 添加不存在的map键不存在，则新增，存在，则不变  
        boundHashOperations.putIfAbsent("m2","n2-1");
12. size() 获取特定键对应的map大小
        Long size = boundHashOperations.size();
13. scan(ScanOptions options)  扫描特定键所有值
        Cursor<Map.Entry<String, Object>> scan = boundHashOperations.scan(ScanOptions.NONE);
14. delete(Object... keys) 批量删除map值
        long delSize = boundHashOperations.delete("m3","m2");

```
4. boundListOps - 列表操作方法
- 一个列表可以存储 2^32 - 1 键值对（40多亿）。
- 有序
```
1. 定义绑定的键  
		BoundListOperations boundListOperations = redisTemplate.boundListOps("lk");
2. leftPush(V value)和rightPush(V value) 在绑定键中添加值
        boundListOperations.leftPush("h");
        boundListOperations.rightPush("a");
3. range(long start, long end) 获取绑定键中给定的区间值
        List range = boundListOperations.range(0, -1);
4. index(long index) 获取给定位置的值
        Object index = boundListOperations.index(0);
5. leftPop() 弹出左边的值
        Object o = boundListOperations.leftPop();
6. rightPop() 弹出右边的值
        Object o1 = boundListOperations.rightPop();
7. leftPush(V pivot, V value) 在指定的第一个值出现的左边添加值
        boundListOperations.leftPush("i","w");
8. rightPush(V pivot, V value) 在指定的第一个值出现的右边添加值
        boundListOperations.rightPush("i","x");
9. leftPop(long timeout, TimeUnit unit) 在指定的时间后弹出左边的值
        boundListOperations.leftPop(1, TimeUnit.SECONDS);
10. rightPop(long timeout, TimeUnit unit) 在指定的时间后弹出右边的值
        boundListOperations.rightPop(1, TimeUnit.SECONDS);
11. leftPushAll(V... values) 在左边批量添加值
        boundListOperations.leftPushAll("y","g");
12. rightPushAll(V... values) 在右边批量添加值
        boundListOperations.rightPushAll("b","r");
13. leftPushIfPresent(V value) 在左边添加不存在的值
        boundListOperations.leftPushIfPresent("k");
14. rightPushIfPresent(V value) 在右边添加不存在的值
        boundListOperations.rightPushIfPresent("k");
15. remove(long count, Object value) 移除指定个数的值
        boundListOperations.remove(2,"i");
16. set(long index, V value)  在指定位置添加值
        boundListOperations.set(0,"j");
17. trim(long start, long end) 截取原来区间的值为新值
        boundListOperations.trim(1,3);
```
5. boundSetOps - set操作方法
- 一个set可以存储 2^32 - 1 键值对（40多亿）。
- 无序且唯一

```
1. 定义一个BoundSetOperations
        BoundSetOperations boundSetOperations = redisTemplate.boundSetOps("bso");
2. add(V... values)  批量添加值
        boundSetOperations.add("a","b","c");
3. members() 获取所有值
        Set members = boundSetOperations.members();
4. scan(ScanOptions options)  匹配获取键值对，ScanOptions.NONE为获取全部键值对
        Cursor<String> cursor = boundSetOperations.scan(ScanOptions.NONE);
        while (cursor.hasNext()){
            String next = cursor.next();
        }
5. randomMember() 随机获取一个值
		Object o2 = boundSetOperations.randomMember();
6. distinctRandomMembers(long count) 获取唯一的随机数量值
        Set set = boundSetOperations.distinctRandomMembers(2);
7. diff(Collection<K> keys) 比较多个特定键中的不同值
        Set diff = boundSetOperations.diff(new ArrayList());
8. pop() 弹出集合中的值
        Object pop = boundSetOperations.pop();
9. remove(Object... values)
        long removeCount = boundSetOperations.remove("c");  

```
6. BoundValueOperations - string操作方法

```
1. 首先要定义一个BoundValueOperations
        BoundValueOperations boundValueOperations = redisTemplate.boundValueOps("bvo");
2. append(String value) 在原来值的末尾添加值
        boundValueOperations.append("a");  
3. get(long start, long end) 获取指定区间位置的值
        String s = boundValueOperations.get(0, 2);
4. get() 获取字符串所有值
        Object o3 = boundValueOperations.get();
5. set(V value) 给绑定键重新设置
        boundValueOperations.set("f");
6. set(V value, long timeout, TimeUnit unit)
        boundValueOperations.set("wwww",5,TimeUnit.SECONDS);
7. set(V value, long offset) 截取原有值的指定长度后添加新值在后面
        boundValueOperations.set("nnnnnn",3);
8. setIfAbsent(V value) 没有值存在则添加
        boundValueOperations.setIfAbsent("mmm");  
9. getAndSet(V value)  获取原来的值并重新赋新值
        Object yyy = boundValueOperations.getAndSet("yyy");
10. size() 获取绑定值的长度
        Long size1 = boundValueOperations.size();
11. increment(double delta)和increment(long delta)   自增长键值，前提是绑定值的类型是doule或long类型
        Long increment = boundValueOperations.increment(1);

```
7. convertAndSend(String channel, Object message) - 实现队列 发布消息
8. countExistingKeys(Collection<K> keys) - 统计匹配参数中key的个数
9. createRedisConnectionProxy(RedisConnection pm) -  connection代理类
10. delete(Collection<K> keys) - 删除多个key
11. delete(K key) - 删除指定key
12. discard() - 废弃multi()之后的指令
13. dump(K key) - 执行redis dump操作
14. exec() - 使用默认的序列化方法执行事务
15. exec(RedisSerializer<?> valueSerializer) - 使用给定的序列化方法执行事务
16. execRaw() 
17.	execute(RedisCallback<T> action) - 在redis连接中执行指定操作, 对于连接的操作会更全面
18.	execute(RedisCallback<T> action, boolean exposeConnection) - 在redis连接中执行指定操作,这个连接是否会暴露
19.	execute(RedisCallback<T> action, boolean exposeConnection, boolean pipeline) - 在redis连接中执行指定操作,这个连接是否会暴露,是否使用管道
20. execute(RedisScript<T> script, List<K> keys, Object... args) - 执行redis脚本
21. execute(RedisScript<T> script, RedisSerializer<?> argsSerializer, RedisSerializer<T> resultSerializer, List<K> keys, Object... args) - 执行redis脚本,脚本用给定解码器对参数和结果进行序列化
22. execute(SessionCallback<T> session) - 执行redis动作,允许事务
23. executePipelined(RedisCallback<?> action) - redis批处理执行,使用默认的序列化工具
24. executePipelined(RedisCallback<?> action, RedisSerializer<?> resultSerializer) - redis批处理执行,使用给定的序列化工具
25. executePipelined(SessionCallback<?> session) - redis批处理,允许使用事务
26. 	executePipelined(SessionCallback<?> session, RedisSerializer<?> resultSerializer) -  redis批处理执行,使用给定的序列化工具, 允许使用事务
27. expire(K key, long timeout, TimeUnit unit) - 操作指定key的失效时间
28. expireAt(K key, Date date) - 操作执行key的失效时间,使用timestamp
29. getClientList() - 返回客户端的链接数
30. getDefaultSerializer() - 获取这个模板的默认序列化工具
31. getExpire(K key) - 获取指定key还可以活多久 单位秒
32. getExpire(K key, TimeUnit timeUnit) - 获取指定key还可以活多久,单位为指定单位
33. getHashKeySerializer() - 获取序hash key的列化工具
34. getHashValueSerializer() - 获取hash value的序列化工具
35. getKeySerializer() - 获取key的序列化工具
36. getStringSerializer() - 获取string的序列化工具
37. getValueSerializer() - 获取value的序列化工具
38. hasKey(K key) - 判定这个key是否存在
39. isEnableDefaultSerializer()  - 默认华序列化工具是否可以被使用
40. isExposeConnection() - 暴露本地连接给回调code,还是连接代理
41. keys(K pattern) - 返回所有能匹配pattern的key
42. killClient(String host, int port) - 干掉某个链接
43. move(K key, int dbIndex) - 将某个key丢到指定db
44. multi() - 开启事务
45. opsForCluster() - 返回集群的特定操作接口
46. opsForGeo() - 返回地理位置的特定操作接口
47. opsForHash() - 获取hash的数据类型模板 [API>>](https://docs.spring.io/spring-data/redis/docs/current/api/org/springframework/data/redis/core/HashOperations.html)
```
1. 从散列中删除给定的多个元素
    delete(H key, Object... hashKeys)
2. 获取散列的key-value键值对集合
    Map<HK, HV> entries(H key)
3. 得到某个散列中key的hash值
    HV get(H key, Object hashKey)
4. 判断散列中是否存在某个key
    Boolean hasKey(H key, Object hashKey)
5. 为散了中某个值加上增加double数值
    Double increment(H key, HK hashKey, double delta)
6. 为散了中某个值加上增加long数值
    increment(H key, HK hashKey, long delta)
7. 获取散列中所有的key集合
    keys(H key)
8. 得到多个key的值。
    multiGet(H key, Collection<HK> hashKeys)
9. 为散列添加或者覆盖一个 key-value键值对
    put(H key, HK hashKey, HV value) 
10. 为散列添加或者覆盖多个 key-value键值对
    putAll(H key, Map<? extends HK,? extends HV> m)
11. 为散列添加一个key-value键值对。如果存在则不添加不覆盖。返回false
    	putIfAbsent(H key, HK hashKey, HV value)
12. 获取散列的游标
    Cursor<Map.Entry<HK, HV>> scan(H key, ScanOptions options)
```
48. opsForList() - 获取list的数据类型模板 [ API>>](https://docs.spring.io/spring-data/redis/docs/current/api/org/springframework/data/redis/core/ListOperations.html)

```
1. 获取列表中指定索引的value
    index(K key, long index)
2. 移除列表中的第一个值，并返回该值
    leftPop(K key)
3. 阻塞版本的 leftPop(K key) 移除列表的第一个值，并且返回。如果列表为空，则一直阻塞指定的时间单位
    leftPop(K key, long timeout, TimeUnit unit)
4.从key列表左边（从头部）插入值
    leftPush(K key, V value)
5.在key的列表中指定的value左边（前面）插入一个新的value.如果 指定的value不存在则不插入任何值。
    leftPush(K key, V pivot, V value)
6.在key的列表中指定的value左边（前面）插入一个新的value.如果 指定的value不存在则不插入任何值。
    leftPush(K key, V pivot, V value)
7. 从key列表左边（头部）依次插入集合中的值
    leftPushAll(K key, Collection<V> values)
8. 从key列表左边（头部）插入多个值
    leftPushAll(K key, V... values)
9. 如果列表存在，则在列表左边插入值value
    leftPushIfPresent(K key, V value)
10. 获取指定key的范围内的value值的 list列表。 （0  -1）反回所有值列表 
    range(K key, long start, long end)
11. 删除列表中第一个遇到的value值。count指定删除多少个。
    remove(K key, long count, Object value)
12. 设置key列表中指定位置的值为value index不能大于列表长度。大于抛出异常
    set(K key, long index, V value)
13. 获取key 列表的长度
    size(K key)
14. 保留key指定范围内的列表值。其它的都删除。
    trim(K key, long start, long end)
```
49. opsForSet() - 获取set的数据类型模板  [API>>](https://docs.spring.io/spring-data/redis/docs/current/api/org/springframework/data/redis/core/SetOperations.html)

```
1. 添加一个或多个值到某个key的set, 集合不存在创建后再添加
    add(K key, V... values) 
2. 求指定集合与另外多个集合的差集
    difference(K key, Collection<K> otherKeys) 
3. 求指定集合与另一个集合的差集
    difference(K key, K otherKey)
4. 求指定集合与另一个集合的差集，并保存到目标集合
    differenceAndStore(K key, K otherKey, K destKey)
5. 随机返回集合中指定数量的元素。随机的元素不会重复
    distinctRandomMembers(K key, long count)
6. 求指定集合与另一个集合的交集
    intersect(K key, K otherKey)
7. 求指定集合与另多个集合的交集
    intersect(K key, Collection<K> otherKeys)
8. 求指定集合与另多个集合的交集，并且存储到目标集合中
    intersectAndStore(K key, Collection<K> otherKeys, K destKey)
9. 求指定集合与另一个集合的交集，并且存储到目标集合中
    intersectAndStore(K key, K otherKey, K destKey)
10. 检查集合中是否包含某个元素
    isMember(K key, Object o)
11. 获取集合中的所有元素
    members(K key)
12. 把源集合中的一个元素移动到目标集合。成功返回true.
    move(K key, V value, K destKey) //value可为空
13. 随机删除集合中的一个值，并返回。
    pop(K key)
14. 随机删除集合中的多个值，并返回。
    pop(K key, long count)
15. 随机返回一个值
    randomMember(K key)
16. 随机返回多个值
    randomMembers(K key, long count)
17. 移除集合中多个value值
    remove(K key, Object... values)
18. 获取集合的游标。通过游标可以遍历整个集合
    scan(K key, ScanOptions options)
19. 获取集合的大小
    size(K key)
20. 求指定集合与另外多个集合的并集 并返回并集
    union(K key, Collection<K> otherKeys)
21. 球指定集合跟其他集合的并集
    union(K key, K otherKey)
22. 求指定集合与另外多个集合的并集，并保存到目标集合
    unionAndStore(K key, Collection<K> otherKeys, K destKey)
23. 求指定集合与其他集合的并集，并保存到目标集合
    unionAndStore(K key, K otherKey, K destKey)
    
```
50. opsForValue - 获取string操作模板

```
1. 为 key的值末尾追加 value 如果key不存在就直接等于 set(K key, V value)
    append(K key, String value)
2. 将键的整数值减一
    decrement(K key)
3. 将键的整数值减小指定大小
    decrement(K key, long delta)
4. 获取key 值从 start位置开始到end位置结束。 等于String 的 subString 前后闭区间 0 -1 整个key的值 -4 -1 从尾部开始往前截长度为4
    String get(K key, long start, long end)
5. 根据 key 获取对应的value 如果key不存在则返回null
    get(Object key)
6. 设置key的值为value 并返回旧值。 如果key不存在返回为null
    getAndSet(K key, V value
7. 获取键对应值的ascii码的在offset处位值
    getBit(K key, long offset)
8. 将键的整数值加一
	increment(K key)
9. 为key 的值加上 long delta. 原来的值必须是能转换成double类型的。否则会抛出异常
    increment(K key, double delta)
10. 为key 的值加上 long delta. 原来的值必须是能转换成long类型的。否则会抛出异常
	increment(K key, long delta)
11. 根据提供的key集合按顺序获取对应的value值
    multiGet(Collection<K> keys)
12. 设置多个键值对
    multiSet(Map<? extends K,? extends V> map)
13. 设置多个键值对, 如果存在,就不设置
    multiSetIfAbsent(Map<? extends K,? extends V> map)
14. 设置 key 的值为 value 如果key不存在添加key 保存值为value如果key存在则对value进行覆盖
	set(K key, V value)
15. 将value从指定的位置开始覆盖原有的值。如果指定的开始位置大于字符串长度，先补空格在追加.如果key不存在，则等于新增。长度大于0则先补空格set("key10", "abc", 3) 得到结果为：3空格 +"abc"
    set(K key, V value, long offset)
16. 设置 key 的值为 value 其它规则与 set(K key, V value)一样
    set(K key, V value, long timeout, TimeUnit unit)
17. 设置key的值偏移量为offset的bit位上的值为0或者1.true:1 false:0
    setBit(K key, long offset, boolean value)
18. 如果key不存在，则设置key 的值为 value. 存在则不设置设置成功返回true 失败返回false
    setIfAbsent(K key, V value)
19. 只有key存在时才对这个key设置
    setIfPresent(K key, V value)
20. 获取key对应值的长度
    size(K key)
    	
```
51. persist(K key) - 干掉失效时间
52. randomKey() - 返回一个随机建
53. rename(K oldKey, K newKey) - 将key的旧名改成新名
54. renameIfAbsent(K oldKey, K newKey) - 在新名字不存在的情况下,将key的旧名改成新名
55. redis恢复指令restore(K key, byte[] value, long timeToLive, TimeUnit unit, boolean replace)
56. setBeanClassLoader(ClassLoader classLoader) - 当没有其他默认的redis序列化工具的时候设置使用默认的jdk序列化工具
57. setDefaultSerializer(RedisSerializer<?> serializer) - 设置该工具为默认的序列化工具
58.	setEnableDefaultSerializer(boolean enableDefaultSerializer) - 设置启用默认序列化工具
59.	setEnableTransactionSupport(boolean enableTransactionSupport) - 启用事务
60.	setExposeConnection(boolean exposeConnection) - 设置是否暴露redis链接给回调服务
61.	setHashKeySerializer - 设置hash key的序列化方式
62.	setHashValueSerializer(RedisSerializer<?> hashValueSerializer) - 设置hash值的序列化方式
63.	setKeySerializer(RedisSerializer<?> serializer) - 设置key的序列化方式
64.	setScriptExecutor(ScriptExecutor<K> scriptExecutor)  - 设置脚本执行器
65.	setStringSerializer(RedisSerializer<String> stringSerializer) - 设置string的序列化方式
66.	setValueSerializer(RedisSerializer<?> serializer) - 设置value的序列化方式
67.	slaveOf(String host, int port) - 可以将当前服务器转变为指定服务器的从属服务器
68. slaveOfNoOne() - 使得这个从属服务器关闭复制功能
69. sort(SortQuery<K> query) - 对查询进行排序
70. sort(SortQuery<K> query, BulkMapper<T,S> bulkMapper, RedisSerializer<S> resultSerializer) - 对查询结果排序
71. type(K key) - 返回key值的类型
72. unlink(Collection<K> keys) - 删除key列表
73. unlink(K key) - 删除key
74. watch(K key) - 监视在事务范围内的key
75. unwatch() - 解除监视