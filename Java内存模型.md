#### Java内存模型

![image-20200720202831407](C:\Users\winv87\AppData\Roaming\Typora\typora-user-images\image-20200720202831407.png)

- 可以看到线程栈中，存在一个属于当前线程的工作内存，此工作内存数据为主内存数据的私有拷贝。工作内存一般为CPU高速缓存和CPU寄存器，拥有远大于主内存的运行速度。



- Object3分配在堆内存中，且两个线程栈都持有此对象的引用。 都可以调用Object3的成员变量，每个线程都持有这个**成员变量的私有拷贝**

- 如果持有Object3的两个线程进行数据读取，首先其中一个工作线程需要将本地内存持有的Object3刷新到主内存中，然后另外一个工作线程从主内存中读取。



![image-20200721205257496](C:\Users\winv87\AppData\Roaming\Typora\typora-user-images\image-20200721205257496.png)

速度：CPU寄存器 > CPU高级缓存  > 主内存

TIP: **volatile**关键字，在C语言的表示含义为：禁止使用CPU缓存。  Java语言表示属性修改后，主内存立即可见。

这就意味着线程在读写volatile修饰的属性时，一直与主内存进行交互。





![image-20200721210851679](C:\Users\winv87\AppData\Roaming\Typora\typora-user-images\image-20200721210851679.png)

- 以上过程一定是顺序执行。
- 加锁和解锁是作用于主内存的。
- 如果要执行解锁unlock，必须将工作内存中的数据同步到主内存。
- 变量的产生只能在主内存发生，不允许在工作内存中产生。

