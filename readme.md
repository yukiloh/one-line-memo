# one line memo
个人使用的单行笔记,用于记录一些碎片化的编程知识  
虽然大部分都不可能用一行就能说明

## Java

### 多线程

#### join,sleep(0),yield的区别

- `join`  : 该线程完成后才会执行后续的线程  
```java
//假设有3条线程
thread1.start();
thread2.start();
thread2.join();
thread3.start();
```
线程1和2可能会出现轮流执行,但是只有当线程2完成后才会执行线程3

- `sleep(0)` : 使当前线程放弃执行权,并重新触发系统级别cpu竞争(据说只会调度大于等于当前线程的优先级的线程,未考证)
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
sleep(1)也可以实现相同效果,但实际sleep(1)容易消耗更多时间(比如20ms).  


- `yield` : 基本与sleep(0)类似,唯一的区别使sleep()可以抛出InterruptedException.
各版本jvm的不同sleep(0)会产生不同的效果,大体上可以理解为者一致.

※sleep和yield都不会释放当前锁
※奇怪的知识(未测试),执行一个while(true)任务时,yield和sleep(0)都会使得cpu占用100%,但sleep(1)不会

#### volatile ['vɑlət(ə)l]  和 synchronized
首先需要了解cpu和1,2,3级缓存,cpu每次会优先去缓存中读值,而不是主存(内存)  
volatile会标记变量,让cpu(遵循MESI协议)每次都去主存获取值(保证每次都是最新的值),但他不保证变量的原子性  
而synchronized会直接锁住对象,虽然能保证原子性,但会造成阻塞(volatile并不会阻塞)  
另外,synchronized的对象会被优化(gc),而volatile不会  
(目前大多数版本的jvm)在cpu空闲时也会尽力保证去更新主存的值,当加了volatile关键词后才会是一定更新

volatile的适用场景:
- 对变量的写入操作不依赖变量的当前值,或者确保只有单线程去更新变量
- 变量没有被,有其他变量的对象所引用(绕口,例如假设a被volatile关键词修饰: int c = b + a,这种是不适用的)