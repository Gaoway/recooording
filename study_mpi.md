MPI全称是message passing interface，即信息传递接口，是用于跨节点通讯的基础软件环境。

MPI的编程方式，是“一处代码，多处执行”。编写过多线程的人应该能够理解，就是同样的代码，不同的进程执行不同的路径。我们可以同时启动一组进程，在同一个通信域中不同的进程都有不同的编号，程序员可以利用MPI提供的接口来给不同编号的进程分配不同的任务和帮助进程相互交流最终完成同一个任务。

MPI有几个常用的API，其中有一个叫做MPI_Comm_rank, 它返回调用它的进程的具体进程号。比如我启动了10个进程，然后每个进程的这个调用会返回0-9对应的进程编号。

运行时还要用特殊的程序，比如mpiexec和mpirun。

### 点对点通信
mpi4py提供了点对点通信的接口使得多个进程间能够互相传递Python的内置对象（基于pickle序列化），同时也提供了直接的数组传递（numpy数组，接近C语言的效率）。

如果我们需要传递通用的Python对象，则需要使用通信域对象的方法中小写的接口，例如send(),recv(),isend()等。

如果需要直接传递数据对象，则需要调用大写的接口，例如Send(),Recv(),Isend()等，这与C++接口中的拼写是一样的。

``` 
from mpi4py import MPI
import numpy as np
comm = MPI.COMM_WORLD
rank = comm.Get_rank()
size = comm.Get_size()
if rank == 0:
    data = range(10)
    comm.send(data, dest=1, tag=11)
    print("process {} send {}...".format(rank, data))
else:
    data = comm.recv(source=0, tag=11)
    print("process {} recv {}...".format(rank, data))

```

### 组通信

#### 广播
```                                            
comm = MPI.COMM_WORLD                                                      
rank = comm.Get_rank()                                                     
size = comm.Get_size()                                                     
                                                                           
if rank == 0:                                                              
    data = range(10)                                                       
    print("process {} bcast data {} to other processes".format(rank, data))
else:                                                                      
    data = None                                                            
data = comm.bcast(data, root=0)                                            
print("process {} recv data {}...".format(rank, data))
```

#### 发散
发散可以向不同的进程发送不同的数据，而不是完全复制。

```                                                            
comm = MPI.COMM_WORLD                                                             
rank = comm.Get_rank()                                                            
size = comm.Get_size()                                                            
                                                                                  
recv_data = None                                                                  
                                                                                  
if rank == 0:                                                                     
    send_data = range(10)                                                         
    print("process {} scatter data {} to other processes".format(rank, send_data))
else:                                                                             
    send_data = None                                                              
recv_data = comm.scatter(send_data, root=0)                                       
print("process {} recv data {}...".format(rank, recv_data))
```

#### 收集
收集过程是发散过程的逆过程，每个进程将发送缓冲区的消息发送给根进程，根进程根据发送进程的进程号将各自的消息存放到自己的消息缓冲区中。

```
comm = MPI.COMM_WORLD                                               
rank = comm.Get_rank()                                              
size = comm.Get_size()                                              
                                                                    
send_data = rank                                                    
print "process {} send data {} to root...".format(rank, send_data)  
recv_data = comm.gather(send_data, root=0)                          
if rank == 0:                                                       
    print "process {} gather all data {}...".format(rank, recv_data)
```

多机多核: 加一个host文件，里面写上所有节点ip，以mpirun -n 3 -f host python run.py运行,run.py在每个节点的路径要相同，节点配置也要相同

## python异步函数
```
import asyncio

async def myfun(i):
    print('start {}th'.format(i))
    await asyncio.sleep(1)
    print('finish {}th'.format(i))

loop = asyncio.get_event_loop()
myfun_list = (myfun(i) for i in range(10))  //myfun_list = [asyncio.ensure_future(myfun(i)) for i in range(10)]
loop.run_until_complete(asyncio.gather(*myfun_list)) //loop.run_until_complete(asyncio.wait(myfun_list))
```

多线程、多进程相比，asyncio模块有一个非常大的不同：传入的函数不是随心所欲

myfun函数中的sleep换成time.sleep(1)，运行时则不是异步的，而是同步，共等待了10秒

