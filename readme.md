# one line memo

个人使用的单行笔记,用于记录一些碎片化的CS知识  
虽然大部分都不可能用一行就能说明  
后期随着内容增多可能会进行分类  

## Java

### 多线程

#### 原子性(Atomic),可见性(Visibility),有序性(Ordering)

基础概念,知道有这3个东西就行了,后期写代码时候自己会理解的

---

#### 线程的5个状态

- 新建状态：未调用start()前
- 就绪状态：调用start().另外sleep后,或wait中被唤醒也是就绪状态
- 运行状态：执行run()方法时
- 阻塞状态：sleep中
- 死亡状态：线程执行完毕

---

#### join,sleep(0),yield的区别

- `join`  : 父线程会等待执行join的子线程(父线程挂起),当子线程执行完后才会唤醒父线程,继续执行之后的代码  

```java
//假设有3条线程
thread1.start();
thread2.start();
thread2.join();
thread3.start();
```

线程1和2可能会出现轮流执行,但是只有当线程2完成后才会执行线程3

- `sleep(0)` : 使当前线程放弃执行权,并重新触发系统级别cpu竞争  
(据说只会调度大于等于当前线程的优先级的线程,未考证)

```java
//thread1对象
Thread thread1 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    System.out.println("111");
                    try {
                        //这里执行了sleep(0)
                        Thread.sleep(0);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        });

//thread2省略

thread1.start();
thread2.start();
```  

可以试一下没有sleep(0)和有sleep(0)所打印的结果  
有sleep(0)的打印111/222会更平均  
sleep(1)也可以实现相同效果,但实际sleep(1)容易消耗更多时间(比如20ms)  

- `yield` : 基本与sleep(0)类似,唯一的区别使sleep()可以抛出InterruptedException  
各版本jvm的不同sleep(0)会产生不同的效果,大体上可以理解为者一致  

※sleep和yield都不会释放当前锁(与sleep相对的,wait会释放锁)  
※奇怪的知识(未测试),执行一个while(true)任务时,yield和sleep(0)都会使得cpu占用100%,但sleep(1)不会  

---

#### volatile ['vɑlət(ə)l]  和 synchronized

首先需要了解cpu和1,2,3级缓存,cpu每次会优先去缓存中读值,而不是主存(内存)  
volatile会标记变量,让cpu(遵循MESI协议)每次都去主存获取值(保证每次都是最新的值),但他不保证变量的原子性  
而synchronized会直接锁住对象,虽然能保证原子性,但会造成阻塞(volatile并不会阻塞)  
另外,synchronized的对象会被优化(gc),而volatile不会  
(目前大多数版本的jvm)在cpu空闲时也会尽力保证去更新主存的值,当加了volatile关键词后才是**一定**更新  

volatile的适用场景:

- 对变量的写入操作不依赖变量的当前值,或者确保只有单线程去更新变量  
- 变量没有被存在其他变量的对象所引用  
(有点绕口,假设a被volatile关键词修饰: int c = b + a,这种情况是不适用的)  

---

#### 可重入锁与不可重入锁

把可重入锁记忆为**递归锁**更确切  
首先,锁有一个状态值state,0的时候表示无线程在使用,1表示有  
不可重入锁state最多为1  
可重入锁可以使当前线程获取多次获取锁,state可以累加超过1  
比如类下方法进行了synchronized,而同1类下的内部类又进行了synchronized就会出现递归锁的情况  
可重入锁主要为了避免死锁,试想父类获取锁后,内部类又要获取锁,此时如果是不可重入的话内部类就会原地傻等,产生死锁  

※ 除了**synchronize**和**ReentrantLock**及子类,其他都是不可重入锁  

---

#### synchronized 和 ReentrantLock的区别

| 类别   | synchronized                           | ReentrantLock            |
|------|----------------------------------------|--------------------------|
| 存在形式 | 关键词                                    | 类                        |
| 锁的获取 | A线程获取后,B会等待;如果A发生阻塞,则B会一直等待            | 多种情况,大致概括为会尝试获取锁,并不会一直等待 |
| 锁的释放 | 1\.锁住的线程执行完同步代码后释放;2\.线程执行发生异常时jvm自动释放 | 必须在finally中释放            |
| 锁的状态 | 不可判断                                   | 可以判断                     |
| 锁的类型 | 可重入 不可中断 非公平                           | 可重入 可判断 可公平/非公平          |
| 性能   | 少量同步                                   | 大量同步                     |

synchronized是悲观锁,而Lock是乐观锁(通过CAS实现compare-and-swap)  
在lock出现后,synchronized的性能低于其,但1.6,1.7后被优化,性能与Lock无差  
因此1.6之后的**首先推荐**使用synchronized

#### CAS

Compare and Swap ,比较后交换  
一种无锁算法,比较预期值和新值,当二者相同时将现有的内存值与新值交换  
拿sql举例:
> 执行sql,预计版本号会从 0 到 1
> 插入数据后,比较现有版本号
> 如果是1,则提交事务
> 如果不是1,说明执行sql期间其他线程也执行了sql,进行回滚

---

#### synchronized 的优化

主要通过:自旋锁,锁消除,锁粗化,和轻/重量级锁,偏向锁来优化锁  

- 自旋锁和适应性自旋:  
  线程的唤醒和阻塞会极大的影响cpu的开销  
  且大部分的情况下,线程在放弃锁后立刻又会去获取锁  
  因此设置了自旋锁,让线程进入无意义的等待后立刻再去获取锁  
  同时为了让虚拟机更聪明(减少无意义等待的时间),又引入了适应性自旋  
  假如某个锁经常不会放弃后立刻获取,那么就会减少或停止线程自旋  
  
- 锁消除:  
  在编译阶段移除不必要的同步  
  不光是开发者写的代码,一些系统级别的方法也会用到同步  
  编译器会判断如果没有线程安全问题则移除他的锁  

- 锁粗化:  
  比如StringBuffer在append时会进行加锁解锁  
  因此,如果存在多次连续加锁、解锁操作则只需要在第一次和最后一次加锁解锁即可  

- 至于后3个,属于synchronized锁膨胀的内容,另外说明  

---

#### synchronized 的锁膨胀(无状态锁,偏向锁,轻量级锁,重量级锁的形成)

实际的原理比较复杂,这里简单记忆即可  
※ 除了无状态锁和偏向锁可以互相转换降级外,其他类型的锁只可以升级,不可降级  

- 无状态锁:  
  通过CAS实现.实际上CAS也是无状态锁的实现

- 偏向锁:  
  锁一直被同一线程获取时,会形成偏向锁(为了减少获取锁时的开销)  
  
  - 关于设置锁的过程:  
  1. 在Mark Word设置为偏向锁(01)  
  2. 在Mark Word设置线程id为当前线  
  如果期间有另外的线程来竞争则升级为轻量级锁  
  
- 轻量级锁:  
  当偏向锁被其他线程访问时形成  
  如果无法获取当前锁则进行自旋等待  

- 重量级锁:  
  轻量级锁自旋后,依然无法获取则升级为重量级锁  
  此时获取该锁的线程会进入阻塞  
  
---

#### 公平锁

**公平锁锁住的是一个线程队列**
java可以通过ReentrantLock来实现公平锁(synchronized只可以是非公平锁)  
而ReentrantLock的底层又通过队列的FIFO来实现公平锁(未考证)  
这个队列储存着线程,每次会从队列头部取出一个线程来执行业务  

因此,**公平锁锁住的是一个线程队列**

```java
    private ReentrantLock lock = new ReentrantLock();   //构造函数中可以传入true来控制是否为公平锁
    private int count = 0;      //用于演示锁住的变量

    @Test
    void testContext(){
        //创建一个runnable
        Runnable runnable = () -> {
            System.out.println(Thread.currentThread().getName()+"启动");
            myLock();
        };

        //创建并启动线程
        for (int i = 0; i < 5; i++) {
            new Thread(runnable).start();
        }
    }

    //具体锁的业务
    private void myLock() {
        for (int i = 0; i < 10; i++) {
            try {
                lock.lock();
                count++;
                System.out.println(Thread.currentThread().getName() + "获得了锁,count: " + count);
            }finally {
                lock.unlock();
            }
        }
    }
```

运行后会有2种结果:  

- 公平锁时: 每个线程都会轮流执行count++,而且他们**每次**的顺序都是一样的  

```vb
Thread-0获得了锁,count: 1
Thread-4获得了锁,count: 2
Thread-2获得了锁,count: 3
Thread-1获得了锁,count: 4
Thread-3获得了锁,count: 5
Thread-0获得了锁,count: 6
Thread-4获得了锁,count: 7
Thread-2获得了锁,count: 8
Thread-1获得了锁,count: 9
Thread-3获得了锁,count: 10
Thread-0获得了锁,count: 11
...
```

- 非公平锁时: 可能会被单独某一条线程霸占

```vb
Thread-2获得了锁,count: 1
Thread-2获得了锁,count: 2
Thread-2获得了锁,count: 3
Thread-2获得了锁,count: 4
Thread-2获得了锁,count: 5
...
```

两种锁的区别与用途:  

- 非公平锁性能高于公平锁  
- 如果追求线程队列可控,则使用公平锁
- 如果追求性能,则使用非公平锁  

---

#### 独享锁和共享锁

他俩是一个概念,具体实现非常麻烦,略过

- 独享锁  
  只可以被1个线程持有(上锁),可以写入或读取数据  
  synchronized和Lock都是排他锁

- 共享锁  
  可以被多个线程持有,只可以读取数据  

---

#### AQS (简单了解)

全称`AbstractQueuedSynchronizer`,jdk提供的**构建锁和同步器**的框架  
ReentrantLock就是通过它来创建的

##### AQS核心思想

如果请求的共享资源空闲,则将当前请求资源的线程设置为有效的工作线程,并且将共享资源设置为锁定状态  
如果请求的共享资源被占用,则需要创建一套线程阻塞等待,以及被唤醒时锁分配的机制  
AQS通过CLH列锁实现,即将暂时获取不到锁的线程加入到队列中

##### 关于CLH(Craig,Landin,and Hagersten)

一个隐式的虚拟队列(可以FIFO),内部存储3个变量: 1.前节点,2.当前节点,3.lock的状态(true/false)  
创建时,每个新节点(线程)通过cas尝试去充当tail(获取前节点,并设置自己节点)  
取出时(解锁),设置节点中的lock为false.后节点会观测前节点,如果是false,则表示自己可以进行解锁  
参考: https://www.jianshu.com/p/5ad8539e25c3

---

### JVM

#### pass

---

## 其他

### idea

#### 无法连接GitHub,Connection reset
主要是墙的问题.通过修改host可以解决.注意域名不是`github.com`而是`api.github.com`

#### 为文件添加头部
Settings → Editor → File and Code Templates → File Header中进行设置  
或者添加Live Templates
参考: https://blog.csdn.net/qq_39098813/article/details/80731698
