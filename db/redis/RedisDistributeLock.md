# Redis实现分布式锁
```java
/**
 * Redis实现的分布式锁
 * 基于Redis中的setnx（SET if Not eXist）和expire命令实现
 */
public class DistributeLock {

    private static final String SUCCESS_LOCK = "OK";
    private static final String SET_IF_NOT_EXIST = "NX";
    private static final String SET_WITH_EXPIRE_TIME = "PX";
    private static final Long RELEASE_SUCCESS = 1L;
    /**
     * 进程想要获得锁，就向redis中插入数据，保证值为当前线程独有。
     * 如果redis中已经存在要插入的key，说明已经有线程获得了锁，此时方法可以直接返回false。
     * 设置key保存的时间，可以避免死锁。
     * @param jedis Jedis客户端
     * @param lockKey 即将插入到redis中的key，利用key的唯一性作为标志来表示锁的状态
     * @param requestID 即将插入到redis中的value，作为插入者的标识，表示当前锁已经被当前用户获得
     * @param expiresTime 锁存在的时间
     * @return 成功获得锁将返回true，其他条件均返回false
     */
    public static boolean tryAndGetDistributeLock(Jedis jedis, String lockKey, String requestID, int expiresTime) {
        String result = jedis.set(lockKey, requestID, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expiresTime);
        if (SUCCESS_LOCK.equals(result))
            return true;
        return false;
    }
    /**
     * 使用了lua脚本来判断是否可解锁。条件为：redis中保存了与锁相关的key并且value为当前线程所用的值。
     * lua脚本保证了原子性。
     * 如果不是使用lua脚本，将命令拆解。将会出现删除了不属于本线程的锁
     */
    public static boolean releaseDistributeLock(Jedis jedis, String lockKey, String requestID) {
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        /*
        将单个值转换成合集。Collecitons.singletonList(lockkey);
         */
        Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestID));
        if (RELEASE_SUCCESS.equals(result)) {
            return true;
        }
        return false;
    }
}
```
代码：https://github.com/T00OM/Redis