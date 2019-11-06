### Redis

#### Redis的安装

首先去官网，下载安装包

https://redis.io/download

下载完成后，

```cmd
$ tar xzf redis-5.0.5.tar.gz
$ cd redis-5.0.5
$ make
```

当然`make`的时候，可能会出错，

```
cd src && make all

make[1]: Entering directory '/xx/xx/redis-x.x.x/src'

         CC adlist.o

/bin/sh: cc: command not found

make[1]:  *** [adlist.o] Error 127

make[1]: Leaving directory '/xx/xx/redis-x.x.x/src'

make: *** [all] Error 2
```

如果出现带有`cc`的错误你可能缺少插件，运行

```cmd
yum -y install gcc-c++
```

然后make可能还会出现错误，例如

```
zmalloc.h:50:31: 致命错误：jemalloc/jemalloc.h：没有那个文件或目录
```

运行

```
make MALLOC=libc
```

后，再次make

会发现`src`多出文件。

然后我们

```
./redisServer 
```

运行

#### 关于启动时候 reids的报错问题

```cmd
(error) DENIED Redis is running in protected mode because protected mode is enabled, no bind address was specified, no authentication password is requested to clients. In this mode connections are only accepted from the lookback interface. If you want to connect from external computers to Redis you may adopt one of the following solutions: 1) Just disable protected mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to make this change permanent. 2) Alternatively you can just disable the protected mode by editing the Redis configuration file, and setting the protected mode option to 'no', and then restarting the server. 3) If you started the server manually just for testing, restart it with the --portected-mode no option. 4) Setup a bind address or an authentication password. NOTE: You only need to do one of the above things in order for the server to start accepting connections from the outside.
```

这个问题我们需要去redis.conf文件中修改配置。

首先将本地连接注释掉

```conf
#bind 127.0.0.1
```

然后不让他以守护线程的意思进行运行，yes是启动守护线程

```
daemonize no
```

保护模式，关掉它

```
protected-mode no 
```

为了强制应用配置文件，我们启动的时候加上配置参数

```
./redis-server ../redis.conf
```

#### 指令说明

