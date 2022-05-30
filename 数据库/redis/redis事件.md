# 事件

**文件事件**

Redis服务器通过套接字与客户端和其他服务器连接,文件事件就服务器对套接字的抽象.

**时间事件**

时间事件是服务器对定时操作(类似ServCron)的抽象.

## 文件事件

Redis基于<font color='red'>Reactor模式</font>开发了文件事件处理器

> 处理器使用<font color='red'>I/O多路复用程序</font>监听多个套接字,并根据执行的任务为套接字关联不同事件处理器
>
> 当监听的套接字进行<font color='cornflowerblue'>连接应答,读取,写入,关闭</font>等操作时就会产生对应事件,与套接字关联的事件处理器就会处理事件.

### 文件事件处理器构成

文件事件处理器由4部分组成:套接字,I/O多路复用程序,文件事件分派器和事件处理器.

- **套接字**执行操作会产生文件事件,多个套接字可能会并发产生文件事件.

- **I/O多路复用程序**监听套接字,并将产生文件事件的套接字放在<font color='red'>队列</font>中,最终有序,同步地一次一个将文件事件传送给文件事件分派器.

- **文件事件分派器**根据文件事件类型调用不同的**事件处理器**.

<img src="C:/Users/athement/AppData/Roaming/Typora/draftsRecover/assets/image-20210415210528897.png" alt="image-20210415210528897" style="zoom: 50%;" />

### I/O多路复用程序的实现

redis在不同系统都实现了I/O多路复用程序的API,程序在编译时会自动选择系统中性能最高的I/O多路复用行数.

常见的命令为`select`,`epoll`,`evport`,`kqueue`

<img src="C:/Users/athement/AppData/Roaming/Typora/draftsRecover/assets/image-20210415212106929.png" alt="image-20210415212106929" style="zoom:50%;" />

### 事件的类型

AE_READABLE事件(套接字可读):

> 客户端执行write,close
> 可应答的(acceptable)套接字出现

AE_WRITABLE事件(套接字可写):

> 客户端执行read

同一个套接字同时可读又可写时,服务器<font color='red'>优先读套接字</font>.

### 常用API

```c
aeCreateFileEvent	关联套接字的事件与事件处理器
aeDeleteFileEvent	取消关联
aeGetFileEvents		返回套接字被监听的事件类型
aeWait				阻塞等待套接字产生事件
```

### 文件事件处理器

**以下服务器顺序执行**

连接应答处理器(AE_READABLE)
命令请求处理器(AE_READABLE)
命令回复处理器(AE_WRITABLE)

**服务器与客户端通信过程**

<img src="C:/Users/athement/AppData/Roaming/Typora/draftsRecover/assets/image-20210415213314904.png" alt="image-20210415213314904" style="zoom:50%;" />

## 时间事件

**时间事件分类**:定时事件(<font color='cornflowerblue'>目前不支持</font>),周期性事件(根据事件函数返回值是否为<font color='red'>AE_NOMORE</font>区分)

**时间事件3要素**:事件id,执行时间戳,事件处理器

### 时间事件实现

所有时间事件置于无序链表(执行时间戳无序)中,时间事件执行器运行时,遍历链表,执行已经到达时间的事件处理器.

<img src="C:/Users/athement/AppData/Roaming/Typora/draftsRecover/assets/image-20210415214334636.png" alt="image-20210415214334636" style="zoom:50%;" />

### API

```c
aeCreateTimeEvent(ms,proc) 创建时间事件,返回事件ID
aeDeleteTimeEvent(ID)	   删除时间事件
aeSearchNearestTimer()	   返回最近的时间事件
processTimeEvents()		   遍历所有时间事件,并处理
```

### serverCron函数

Redis服务器依赖serverCron函数定期对自身资源和状态进行检查和调整.

1. 更新服务器统计信息,内存占用,数据库占用
2. 清理过期键值对
3. 关闭和清理失效连接
4. 进行持久化操作
5. 主从同步

serverCron的执行频率由redis.conf中的hz设置.

## 事件的调度与执行

事件的调度与执行流程:

<img src="C:/Users/athement/AppData/Roaming/Typora/draftsRecover/assets/image-20210416134558877.png" alt="image-20210416134558877" style="zoom:50%;" />

事件的调度与执行由aeProcessEvents负责

```c
def aeProcessEvents(){
    // 1. 获取离当前时间最近的时间事件
    shortest = aeSearchNearestTimer(eventLoop);

    // 2. 获取间隔时间
    timeval = shortest - nowTime;

    // 如果timeval 小于 0，说明已经有需要执行的时间事件了。
    if(timeval < 0){
        timeval = 0
    }

    // 3. 在 timeval 时间内，取出文件事件。
    numevents = aeApiPoll(eventLoop, timeval);

    // 4.处理已产生的文件事件
    processFileEvents()
    
    // 5.处理所有已到达的时间事件
    ProcessTimeEvents()
}
```

<font color='red'>aeApiPoll阻塞最近时间事件的等待时间</font>,避免服务器频繁轮询,也避免阻塞太长时间

processFileEvents会处理阻塞过程中出现的所有文件事件,再执行到时的时间事件<font color='cornflowerblue'>(可能比预期晚)</font>

文件事件与时间事件的处理是<font color='cornflowerblue'>同步,原子,有序</font>的,事件处理是非抢占的,在有需要时主动让出执行权

> 文件事件的命令回复太长可break待下次处理
> 耗时的时间事件可放到子进程中执行