---
layout: page
title: 分布式系统和架构相关
---

### Python LRU
```python
# orderDict是实现
class LRUCache:

    def __init__(self, capacity):
        self.capacity = capacity
        self.queue = collections.OrderedDict()
    
    def get(self, key):
        if key not in self.queue:
            return -1 // 要找的数据不在缓存中返回-1
        value = self.queue.pop(key) # 将命中缓存的数据移除
        self.queue[key] = value # 将命中缓存的数据重新添加到头部
        return self.queue[key]
        

    def put(self, key, value):
        if key in self.queue: # 如果已经在缓存中，则先移除老的数据
            self.queue.pop(key)
        elif len(self.queue.items()) == self.capacity:
            self.queue.popitem(last=False) # 如果不在缓存中并且到达最大容量，则把最后的数据淘汰
        self.queue[key] = value # 将新数据添加到头部

# 双向链表+hash实现
class Node(object):
    def __init__(self, data, pre=None, next=None):
        self.data = data
        self.pre = pre
        self.next = next

class DoubleLink(object):
    def __init__(self):
        self.tail = None
        self.head = None
        self.size = 0

    def insert(self, data):
        if isinstance(data, Node):
            tmp_node = data
        else:
            tmp_node = Node(data)

        if self.size == 0:
            self.tail = tmp_node
            self.head = self.tail
        else:
            self.head.pre = tmp_node
            tmp_node.next = self.head
            self.head = tmp_node
        self.size += 1
        return self.head

    def remove(self, node):
        if node == self.head:
            self.head = self.head.next
            self.head.pre = None
        elif node == self.tail:
            self.tail = self.tail.pre
            self.tail.next = None
        else:
            node.pre.next = node.next
            node.next.pre = node.pre
        self.size -= 1

    
class LRUCache:
    def __init__(self, size):
        self.size = size
        self.hash = {}
        self.link = DoubleLink()

    def set(self, key, value):
        if self.size == self.link.size:
            self.link.remove(self.link.tail)
        if key in self.hash:
            self.link.remove(self.hash.get(key))
        tmp_node = self.link.insert(value)
        self.hash.__setitem__(key, tmp_node)

    def get(self, key):
        tmp_node = self.hash.get(key)
        self.link.remove(tmp_node)
        self.link.insert(tmp_node)
        return tmp_node.data
```

### 怎么确保分布式ID的唯一性？
* 使用UUID：本地生成、太长无序难查询
* 数据库自增id：有序简单、独立实例且数字越大有瓶颈
* 批量生成ID：降低数据库访问压力、存在单点故障
* redisINCR：唯一有序、每天在Redis中生成一个Key，使用`INCR`进行累加

### 设计一个秒杀系统，XX分钟之后自动XX？
秒杀主要解决的问题是**并发写**和**并发读**。

[如何设计一个秒杀系统](https://llchen60.com/%E5%A6%82%E4%BD%95%E8%AE%BE%E8%AE%A1%E4%B8%80%E4%B8%AA%E7%A7%92%E6%9D%80%E7%B3%BB%E7%BB%9F/)

### zookeeper和redis如何是心啊分布式锁？有什么区别？

zookeeper：
1. 客户端调用create()方法创建名为“locknode/guid-lock-”的节点，需要注意的是，这里节点的创建类型需要设置为EPHEMERAL_SEQUENTIAL。
2. 客户端调用getChildren(“locknode”)方法来获取所有已经创建的子节点。
3. 客户端获取到所有子节点path之后，如果发现自己在步骤1中创建的节点是所有节点中序号最小的，那么就认为这个客户端获得了锁。
4. 如果创建的节点不是所有节点中需要最小的，那么则监视比自己创建节点的序列号小的最大的节点，进入等待。直到下次监视的子节点变更的时候，再进行子节点的获取，判断是否获取锁。

redis：
1. 根据lockKey区进行setnx（set not exist，顾名思义，如果key值为空，则正常设置，返回1，否则不会进行设置并返回0）操作，如果设置成功，表示已经获得锁，否则并没有获取锁。
2. 如果没有获得锁，去Redis上拿到该key对应的值，在该key上我们存储一个时间戳（用毫秒表示，t1），为了避免死锁以及其他客户端占用该锁超过一定时间（5秒），使用该客户端当前时间戳，与存储的时间戳作比较。
3. 如果没有超过该key的使用时限，返回false，表示其他人正在占用该key，不能强制使用；如果已经超过时限，那我们就可以进行解锁，使用我们的时间戳来代替该字段的值。
4. 但是如果在setnx失败后，get该值却无法拿到该字段时，说明操作之前该锁已经被释放，这个时候，最好的办法就是重新执行一遍setnx方法来获取其值以获得该锁。

redlock算法解决了**单点问题**：
假设我们有N个Redis节点，N应该是一个大于2的奇数。RedLock的实现步骤:
1. 取得当前时间
2. 使用上文提到的方法依次获取N个节点的Redis锁。
3. 如果获取到的锁的数量大于 （N/2+1）个,且获取的时间小于锁的有效时间(lock validity time)就认为获取到了一个有效的锁。锁自动释放时间就是最初的锁释放时间减去之前获取锁所消耗的时间。
4. 如果获取锁的数量小于 （N/2+1），或者在锁的有效时间(lock validity time)内没有获取到足够的说，就认为获取锁失败。这个时候需要向所有节点发送释放锁的消息。

[redis实现分布式锁](https://www.xilidou.com/2017/10/23/Redis%E5%AE%9E%E7%8E%B0%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81/)

Zookeeper实现简单，但效率较低；Redis实现复杂，但效率较高。

### 什么是一致性hash？
一般在决定查询和插入数据到分布式数据库过程中使用哈希函数进行判断，但是当数据库数量变动的时候，所有数据的位置都会发生改变。这时候就需要一致性哈希。

一致性Hash算法将整个哈希值空间组织成一个虚拟的圆环，如假设某哈希函数H的值空间为$0$-$2^{32}-1$。

对于每个服务器进行hash 就可以确定机器在Hash上的位置。

对于数据，使用同样的方法计算hash值，然后按照顺时针行走，第一台遇到的服务器就是定位到的服务器。

不过，一致性哈希算法在服务节点太少时，容易因为节点分布不均匀而造成数据倾斜。在这种情况下引入了虚拟节点机制，对每一个服务节点计算多个哈希，每个计算结果位置都放置一个虚拟节点。在实际应用中，通常将虚拟节点数设置为32甚至更大，因此即使很少的服务节点也能做到相对均匀的数据分布。

![cPYRM2ZeAOtCi9a](https://i.loli.net/2020/07/01/cPYRM2ZeAOtCi9a.png)

```python
# 主机的节点表示
from hashlib import md5
from bintrees.rbtree import RBTree

def _not_exists(key):
    return key is None or key == -1

def find_upper(root, elem):
    if root is None:
        return -1

    if elem == root.key:
        return root.key
    elif elem < root.key:
        maybe_max = find_upper(root.left, elem)
        if _not_exists(maybe_max):
            return root.key
        return maybe_max
    else:
        maybe_max = find_upper(root.right, elem)
        if _not_exists(maybe_max):
            return -1
        return maybe_max


# 找下限
def find_lower(root, elem):
    if root is None:
        return -1

    if elem == root.key:
        return root.key
    elif elem < root.key:
        maybe_min = find_lower(root.left, elem)
        if _not_exists(maybe_min):
            return -1
        return maybe_min
    else:
        maybe_min = find_lower(root.right, elem)
        if _not_exists(maybe_min):
            return root.key
        return maybe_min


def find_next(root, elem):
    if root is None:
        return None, None
    return find_lower(root, elem), find_upper(root, elem)

class Node(object):
    def __init__(self, host_name, id=None, vhost=None):
        self._id = id
        self.host_name = host_name
        if vhost:
            self._hash_host_name = "{}#{}".format(host_name, vhost)
        else:
            self._hash_host_name = host_name
    def get_id(self, ch_size):
        if self._id:
            return self._id
        return int(md5(self._hash_host_name.encode("utf-8")).hexdigest(), 16)% ch_size

class ConsistHash(object):
    def __init__(self):
        self.size = 0xffff
        self.rbt = RBTree()

    def insert_host(self, host):
        host_id = host.get_id(self.size)
        self.rbt.insert(host_id, host)
    
    def remove_host(self, host):
        host_id = host.get_id(self.size)
        self.rbt.remove(host_id)

    @staticmethod
    def _find_upper(root, elem):
        if root is None:
            return -1

        if elem == root.key:
            return root.key
        elif elem < root.key:
            maybe_max = find_upper(root.left, elem)
            if _not_exists(maybe_max):
                return root.key
            return maybe_max
        else:
            maybe_max = find_upper(root.right, elem)
            if _not_exists(maybe_max):
                return -1
            return maybe_max
    
    def find_host(self, id):
        id %= self.size
        idx = self._find_upper(self.rbt._root, id)
        if idx == -1:  # id larger than max id
            # assert tree is not empty
            return self.rbt.min_item()[1]
        return self.rbt.get_value(idx)
```

### 什么是REST？
一种原则，架构约束原则。
网站即软件。
URI是名词，HTTP请求应当表现为动宾短语。
* GET：读取（Read）
* POST：新建（Create）
* PUT：更新（Update）
* PATCH：更新（Update），通常是部分更新
* DELETE：删除（Delete）

`GET http://{host}/{version}/{resources}/{resource_id}`

非幂等操作需要注意防止重复提交

### 什么是MESI？什么是缓存一致性？
四种缓存段状态的首字母缩写：
* 失效缓存段
* 共享缓存段
* 独占缓存段
* 已修改缓存段

### HASH算法

散列函数，是一种单向密码体制，他从明文到密文的不可逆的过程。

MD4、MD5、SHA

### 什么是ZAB协议？
为了保证写操作的一致性与可用性，ZooKeeper专门设计了一种名为原子广播（ZAB）的支持崩溃恢复的一致性协议。基于该协议，ZooKeeper实现了一种主从模式的系统架构来保持集群中各个副本之间的数据一致性。

Zookeeper客户端会随机连接到Zookeeper集群的一个节点，如果是读请求，就直接从当前节点中读取数据；如果是写请求，那么节点就会向 leader 提交事务，leader 会广播事务，只要有超过半数节点写入成功，该写请求就会被提交（类2PC协议）。

一旦Leader节点无法工作，ZAB协议能够自动从Follower节点中重新选出一个合适的替代者，即新的Leader，该过程即为领导选举。该领导选举过程，是ZAB协议中最为重要和复杂的过程。

[ZAB选举](https://dbaplus.cn/news-141-1875-1.html)
[2](https://juejin.im/post/5b924b0de51d450e9a2de615)

### 如何实现多人在线文档编辑？
主要是解决冲突编辑问题。

* 编辑锁
* GNU diff-patch
* Myer’s diff-patch
* Operational Transformation：编辑操作转换为执行语句
* 分布式 Operational Transformation

### RPC请求的流程？
1. 服务消费方（client）调用以本地调用方式调用服务； 
2. client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体； 
3. client stub找到服务地址，并将消息发送到服务端； 
4. server stub收到消息后进行解码； 
5. server stub根据解码结果调用本地的服务； 
6. 本地服务执行并将结果返回给server stub； 
7. server stub将返回结果打包成消息并发送至消费方； 
8. client stub接收到消息，并进行解码； 
9. 服务消费方得到最终结果。

### RPC的原理是什么？
解决两个问题：
* 解决分布式系统中，服务之间的调用问题。
* 远程调用时，要能够像本地调用一样方便，让调用者感知不到远程调用的逻辑。

![ums4k3eIUhlxX7O](https://i.loli.net/2020/07/01/ums4k3eIUhlxX7O.jpg)

### 用python实现一个rpc
```python
# client

import rpcclient

c = rpcclient.RPCClient()
c.connect('127.0.0.1', 5000)
res = c.add(1,2,c=3)
print(f'res: [{res}]')



class TCPClient(object):
    def __init__(self):
        self.sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    def connect(self, host, port):
        self.sock.connect(host, port)
    
    def send(self, data):
        self.socket.send(data)

    def recv(self, length):
        return self.sock.recv(length)

class RPCStub(object):
    def __getattr__(self. function):
        def _func(*args, **kwargs):
            d = {'method_name': function, 'method_args': args, 'method_kwargs':kwargs}
            self.send(json.dump(d).encode('utf-8'))
            data = self.recv(1024)
            return data

        setattr(self, function, _func)
        return _func


#rpcclient
class RPCClient(TCPClient, RPCStub):
    pass


# server

def add(a, b, c=10):
    sum = a + b + c
    return sum

s = rpcserver.RPCServer()
s.register_function(add) # 注册方法
s.loop(5000) # 传入要监听的端口


# RPCserver

class RPCServer(TCPServer, JSONRPC, RPCStub):
    def __init__(self):
        TCPServer.__init__(self)
        JSONRPC.__init__(self)
        RPCStub.__init__(self)

    def loop(self, port):
        self.bind_listen(port)
        print('Server listen {} ...'.format(port))
        while True:
            self.accept_receive_close()

    def on_msg(self, data):
        return self.call_method(data)

class TCPServer(object):
    def __init__(self):
        self.sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    def bind_listen(self, port):
        self.sock.bind(('0.0.0.0', port))
        self.sock.listen(5)

    def accept_receive_close(self):
        '''获取Client端信息'''
        (client_socket, address) = self.sock.accept()
        msg = client_socket.recv(1024)
        data = self.on_msg(msg)
        client_socket.sendall(data) # 回传
        client_socket.close()


class JSONRPC(object):
    def __init__(self):
        self.data = None

    def from_data(self, data):
        '''解析数据'''
        self.data = json.loads(data.decode('utf-8'))

    def call_method(self, data):
        '''解析数据，调用对应的方法变将该方法执行结果返回'''
        self.from_data(data)
        method_name = self.data['method_name']
        method_args = self.data['method_args']
        method_kwargs = self.data['method_kwargs']
        res = self.funs[method_name](*method_args, **method_kwargs)
        data = {"res": res}
        return json.dumps(data).encode('utf-8')


class RPCStub(object):
    def __init__(self):
        self.funs = {}

    def register_function(self, function, name=None):
        '''Server端方法注册，Client端只可调用被注册的方法'''
        if name is None:
            name = function.__name__
        self.funs[name] = function

```


