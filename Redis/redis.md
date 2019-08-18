## Redis 基础数据结构 

Redis 有 5 种基础数据结构，分别为:`string (字符串`)、`list (列表)`、`set (集合)`、`hash (字典)` 和 `zset (有序集合)`。
Redis`所有的数据结构都是以唯一的 key 字符串`作为名称，然后通过这个唯一 key 值来获取相应的 value 数据。不同类型的数据结 构的差异就在于 value 的结构不一样。

#### string(字符串)
```
	set [key] [value]
	get [key]
	# int
	incr [key]
	# 设置过期
	expire key [time]
```

#### list(列表)
```
	rpush [key] [list]
	#左边出：队列
	lpop
	#右边出：栈
	rpop
```
#### hash(字典)
```
	hset dict [key] [value]
	hgetall dict
```
#### set(集合)
```
	sadd set [key] [value]
	smembers set
	#获取长度，相当于count
	scard set
```
#### zset(有序集合)
```
zadd set [key] [value]
```