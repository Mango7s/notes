#### 序列操作(`list`)
```
append：将对象添加到序列的末尾
extend：将可迭代对象中的每个元素添加到序列中
insert：将对象插入到序列中。list.insert([index], [obj])
```

#### 高阶函数
```
filter：filter(func, [iterable])
sorted：sorted([obj], key=lambda x:x[index])
map：map([iterable], func)
zip：zip(itrerable1, iterable2) 将对应位置的元素打包成元组
all：all([iterable])如果iterable的每个元素都是真，返回True；all([]) 返回True
any：all([iterable])如果iterable中有一个元素是真，返回True；any([]) 返回False
```

#### global(全局变量)、nonlocal(自由变量)

```
global：在函数体内，修改不可变对象，需要使用global将变量声明为全局变量
nonlocal：在闭包区域内，修改不可变对象，需要使用nonlocal将变量声明为自由变量
```

#### 排序(不使用sort)

#### 单例模式

一、使用`__new__`方法，每个实例的id都一样

```python
class singleton():
  
		def __new__(cls, *agrs, *kwargs):
				if not hasattr(cls, '_instance'):
						cls._instance = super().__new__(cls, *agrs, *kwargs)
				return cls._instance
```

二、共享属性

```python
class singleton():
		_attrs = {}
    
    def __new__(cls, *args, *kwargs):
      	instance = super().__new__(cls, *args, *kwargs)
    		instance.__dict__ = cls._attrs
        return instance
```

三、天然的单例（模块）

```python
from module import singleton_instance
```

####  简述多进程、多线程、协程、线程安全

进程是计算机分配资源(cpu、内存)的最小单位，多个进程之间互不干扰。

线程是CPU调度的最小单位，线程基于进程，一个进程至少有一个线程，叫主线程，而多个线程共享内存（数据共享，共享全局变量)。

协程：

​		本质上是流程控制，并不是线程。协程的调度完全由用户控制。协程拥有自己的寄存器上下文和栈。

线程安全：

​		线程安全是指某个[函数](https://zh.wikipedia.org/wiki/函数_(计算机科学))、[函数库](https://zh.wikipedia.org/wiki/函数库)在[多线程](https://zh.wikipedia.org/wiki/多线程)环境中被调用时，能够正确地处理多个[线程](https://zh.wikipedia.org/wiki/线程)之间的[共享变量](https://zh.wikipedia.org/w/index.php?title=共享变量&action=edit&redlink=1)，使程序功能正确完成。

​		线程安全问题多是由于全局变量和静态变量引起的，如果每个线程对于这种共享变量只有读操作而没有写操作，一般来说是线程安全的，如果多个线程同时执行写操作，在不考虑线程同步的情况下很有可能影响线程安全。

#### C3算法

python2的继承采用从左到右深度优先的算法

python3的继承采用C3算法

#### post 和 get的区别?

1.GET是从服务器上获取数据，POST是向服务器传送数据

2.在客户端，GET方式在通过URL提交数据，数据在URL中可以看到，POST方式，数据放置在HTML——HEADER内提交

#### python自省



#### 单下划线双下划线

以单下划线开头的(`_xxx`），代表不能直接访问的类属性，需通过类提供的接口进行访问，不能用“from xxx import *”而导入；
以双下划线开头的(`__xxx`)，代表类的私有成员；
以双下划线开头和结尾的(`__xxx__`)，代表python里特殊方法专用的标识，如 __init__（）代表类的构造函数

#### 可迭代对象、迭代器、生成器

可迭代对象：实现了返回迭代器的`__iter__`方法或者实现了`__getitem__`方法，并且参数是从0开始的索引的对象

迭代器：迭代器是一个可以记住遍历的位置的对象。迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束。标准的迭代器接口有两个方法：`__next__`、`__iter__`

生成器：生成器实现了迭代器协议。只是在需要返回数据的时候使用yield语句。

#### django聚合分组

```python
# 聚合
from django.db.models import Avg
Book.objects.all().aggregate(Avg('price'))
# 分组

```

#### TCP（Transmission Control Protocol）/IP(Intelnet Protocol)

1、三次握手四次挥手
三次握手：
作用：建立连接

2、为什么要三次握手，而不是两次四次
      如果是两次，A端发起连接，那么如果在B端到A端应答分组丢失的情况下，B端认为链接已经建立，会一直发送，A端会一直等待等待，形成死锁

3、tcp与udp区别，应用场景
区别：

- tcp基于连接，udp无连接
- 对于系统资源的要求，tcp多、udp少
- tcp可靠性高、速度慢。udp可靠性低、速度快
- UDP程序结构简单
- tcp流模式，udp数据报模式

4、四层模型，哪四层
      - 应用层（协议有Http,Ftp等）
      - 传输层（协议有TCP,UDP）
      - 网络层（IP协议：端到端）
      - 链路层（协议ARP）

 
