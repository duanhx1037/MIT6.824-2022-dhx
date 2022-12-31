什么是web crawler?
- 目标：抓取所有的web页
- 需要给它一个起始web页
- 它会递归地追踪所有的链接
- 但是对每个页只会抓取一次，并且不会陷入死循环

Crawler的挑战
- 如何利用I/O的并发
    - 网络的延迟比网络的容量更加limiting
    - 要提升每秒抓取的URL个数
        - 使用线程来实现并发
- 对每个URL只抓取一次
    - 避免浪费网络带宽
    - 这样对远端服务器友好
        - 但是需要记住哪些URL被抓取过
- 知道何时停止

介绍了三种解法

Serial crawler 串行crawler
- 通过递归的串行调用来执行一个深度优先搜索
- 使用fetched map来避免重复，中止循环
    - 一个简单的字典，通过引用传递，调用者能看到被调用者对它的更新
- 但是一次只抓取一个页面，比较慢

ConcurrentMutex crawler 并发锁 crawler
- 为每一个page的抓取开启一个线程
- ```go func```创建并开启一个goroutine
- 线程之间共享fetched map
- 使用锁（```Lock()```和```Unlock()```）的原因
    - 原因一：
        - 两个进程可能同时fetch同一个URL
        - 都看到该URL是没有被访问的，从而产生错误
        - 锁可以避免这种情况
    - 原因二：
        - map通常是以一种复杂的数据结构实现的
        - 并发的updata/update可能会破坏内部的不变量（wreck internal invariants）
        - 并发的update/read可能会导致读崩溃
- ConcurrentMutex crawler如何决定应该中止？
    - sync.WaitGroup



