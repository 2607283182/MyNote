## 原理
对象头有个mark,这个mark word, 这个markword里有两个重要字段：
锁标志位和占用该锁的threadID
## 特性
| 类别 | synchronized |
| :-----| :----: |
| 存在层次 | JVM层面 | 
| 锁的释放 | 不需要手动释放 | 
| 锁的获取 | 等待线程不能cancel | 
| 锁的状态 | 无法判断 | 
| 锁的类型 | 可重入、不可中断、不可限时、非公平 | 
| 锁的唤醒 | 随机唤醒 | 
## 不可中断原理
sync获取锁的时候 不响应 interruptException

能够被中断的阻塞称之为 轻量级阻塞，不能够被中断的阻塞称之为 重量级阻塞。 而像Object.wait()、Thread.sleep()、Thread.join()、LockSupport.park()这种都是轻量级阻塞，可以被中断唤醒。

所以t.interrupted() 的精确含义是：“唤醒一个轻量级阻塞线程”，而不是 “中断一个线程”。