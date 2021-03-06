NIO，原理，裸写NIO的难点在哪 
````
传统的IO包提供的是同步阻塞IO，
即当用户线程发出IO请求后，内核会去查看数据是否已经就绪，若未就绪，则用户线程会处于阻塞状态（让出CPU），
当数据就绪后，内核会将数据复制到用户线程，并把结果返回给用户线程，同时接触用户线程的阻塞，
同步体现在用户线程需要等待数据就绪后才能向后执行（后面的执行依赖于前面的结果）。

服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，
线程数量也会受到。

而NIO包提供的IO是同步非阻塞IO，
非阻塞体现在用户线程发起IO请求后，会直接得到返回结果，即便在数据未就绪的情况下，也能马上得到失败信息。
而同步体现在用户线程需要主动去轮询直到发现数据就绪，再主动将数据从内核拷贝到用户线程。

服务器实现模式为多个连接一个线程（IO多路复用），即客户端发送的连接请求都会注册到多路复用器上，
多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。

NIO最核心的三个组件
    Channel 通道
    Buffer 缓冲区
    Selector 选择器
    其中Channel对应以前的流，Buffer不是什么新东西，Selector是因为nio可以使用同步的非堵塞模式才加入的东西。
    以前的流总是堵塞的，一个线程只要对它进行操作，其它操作就会被堵塞，也就相当于水管没有阀门，你伸手接水的时候，不管水到了没有，
    你就都只能耗在接水（流）上。
    nio的Channel的加入，相当于增加了水龙头（有阀门），虽然一个时刻也只能接一个水管的水，但依赖轮换策略，在水量不大的时候，
    各个水管里流出来的水，都可以得到妥善接纳，这个关键之处就是增加了一个接水工，也就是Selector，他负责协调，也就是看哪根水管有水了的话，
    在当前水管的水接到一定程度的时候，就切换一下：临时关上当前水龙头，试着打开另一个水龙头（看看有没有水）。
    当其他人需要用水的时候，不是直接去接水，而是事前提了一个水桶给接水工，这个水桶就是Buffer。也就是，其他人虽然也可能要等，但不会在现场等，
    而是回家等，可以做其它事去，水接满了，接水工会通知他们。
    这其实也是非常接近当前社会分工细化的现实，也是统分利用现有资源达到并发效果的一种很经济的手段，而不是动不动就来个并行处理，
    虽然那样是最简单的，但也是最浪费资源的方式。

````












