# Redis概述

sql: 关系型数据库

nosql: 非关系型数据库



# Redis常用命令

`KEYS pattern` 查看符合模板的所有key

`del key` 删除某个key,返回值为成功删除的key的数量

`exists key` 判断一个key是否存在

`expire key 秒数` 给某个key设置存活秒数

`ttl key` 查看key的存活秒数,-1表示永杰存活,-2表示已经销毁



# redis数据结构



## String

String根据value的内容不同可分为string,int和float

`set key value` 添加或修改String类型的键值对

`get key` 根据key获取String类型的键值对

`mset key value...` 批量添加多个String类型的键值对

`mget key...` 根据多个key获取多个String类型的键值对

`incr key` 将一个int类型的key自增1

`incrby key 自增步长` 将一个int类型的key自增指定步长

`incrbyfloat key 自增步长` 将一个float类型的key自增长指定步长

`setnx key value` 添加一个String类型的键值对,如果key已存在则无法添加

`setex key value 有效期` 添加一个Stirng类型的键值对,并指定其有效期





## hash

hash类型的value为一个或多个键值对

`hset key field value` 为某个key添加或修改一个字段,或添加一个新的key

`hget key field` 获取某个key的某个字段

`hmset key field value...` 为某个key批量添加多个字段

`hmget key field...` 批量获取某个key的多个字段

`hgetall key` 获取某个key的所有字段

`hkeys` 获取某个key的所有字段名

`hvalues` 获取某个key的所有字段值

`hincrby key field 自增步长` 是某个key的某个字段值自增

`hsetnx key field` 为某个key添加一个字段(不可修改字段)





## list

list类型的value为一个双向链表和双端队列

`lpush key value...` 添加一个key,或为某个key所代表的链表左侧插入任意个value 

`lpop key count` 从某个key对应的链表左侧移除并返回指定数目个value

`rpush key value...` 与lpush同理

`rpop key count` 与lpop同理

`lrange key start end` 返回某个key对应链表的某个下标范围内的value(不删除value)

`blpop key timeout` 同lpop,但是默认移除一个value,且阻塞式执行 



## set

set类型的value类似于java中的hashset,即为没有value的map,无序存储,key唯一

`sadd key value...` 添加一个key,或向指定key中添加任意个value

`srem key value` 删除指定key的指定value

`scard key` 返回某个key的元素个数

`sismumber key value` 查看某个value是否为某个key的value

`smembers` 获取某个key中的所有元素

`sinter key1 key2` 获取两个key的交集

`sdiff key1 key2` 获取两个key的差集,即key1中有,但key2中没有的value



## sortedset

其value为一个可排序的set

`zadd key score mumber` 添加一个新的key,或为某个key对应的set添加一个value,score为该value的排序依据

`zrem key member` 删除某个key中的某个value

`zcore key member` 获取某个key中某个value的score值

`zrank key member` 获取某个key中指定value的排名

`zcard key` 获取某个key中value的个数

`zrange key min max` 获取score从小到大排序后的第min到第max个元素

`zrevrange key min max` 获取score从小到大排序后的第min到第max个元素

`zrangebyscore key min max LIMIT offset count` min和max表示要获取的分数范围,offsets表示偏移量,count表示要查询的个数





# jedis

jedis为在java中操作redis的框架，导入maven坐标来使用jedis

```java
// 建立连接,主机名为linux系统的ip地址,端口为6379
Jedis jedis =  new Jedis("192.168.26.132", 6379);

// 设置redis密码
jedis.auth("122300");

// 选择要操作的库
jedis.select(0);

// 使用jedis对象实例操作redis
jedis.set("str1", "hello1");
String str1 = jedis.get("str1");

// 关闭连接
jedis.close();
```



由于jedis是单线程,因此最好使用连接池来获取jedis实例

```java
public class JedisConnectFactory {
    private static final JedisPool jedisPool;

    static {
        JedisPoolConfig poolConfig = new JedisPoolConfig();
        poolConfig.setMaxTotal(8);
        poolConfig.setMaxIdle(8);
        poolConfig.setMinIdle(0);
        // 创建连接池对象
        jedisPool = new JedisPool(poolConfig, "192.168.26.132", 6379, 1000, "122300");
    }

    // 通过该方法获取jedis实例
    public static Jedis getJedis() {
        return jedisPool.getResource();
    }
}
```



# spring data redis

spring data redis是spring集成的java操作redis库,在springboot项目中使用



导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```



yml配置

```yaml
spring:
  redis:
    host: 192.168.26.133
    port: 6379
    password: 122300
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        min-idle: 0
        max-wait: 1000ms
```



操作redis

```java
@SpringBootTest
class RedisDemoApplicationTests {

    @Resource
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        redisTemplate.opsForValue().set("str1", "origin");
        Object str1 = redisTemplate.opsForValue().get("str1");
        System.out.println(str1);
    }
}
```



## spring data redis序列化

使用redisTemplate操作redis时,key和value都会被当成Object对象,都需要进过序列化之后,再存入redis中

redisTemplate默认使用ObjectOutputStream来进行序列化,即将对象转为字节数组,这样就导致我们在redis库中存入的实际上是序列化后是字节数组

我们可以配置redisTemplate的序列化方式,即配置key的序列化方式为序列化为String,配置value的序列化方式为序列化为json字符串,如果这样做,name序列化后的json字符串中必须保存该对象的类型才能反序列化,这样就会浪费空间

另一种方案是,将key和value的序列化方式都变为序列化成String,因此对于value来说,先将value转为json字符串,在redis中保存json字符串即可

```java
@SpringBootTest
class RedisDemoApplicationTests {

    // StringRedisTemplate的默认序列化方法就是序列化为json字符串
    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Test
    void contextLoads() {
		User user = new User("jack", 20);
        // 使用fastJson库来进行java对象与json的转换
        String s = JSON.toJSONString(user);
        // value为对象的json字符串
        stringRedisTemplate.opsForValue().set("user", s);
		String jsonString = stringRedisTemplate.opsForValue().get("user");
        // 将json字符串转为java对象
        User user = JSON.parseObject(jsonString, User.class);
        System.out.println(user);
    }
}
```

