# Android面试相关资料

### 内存泄漏原因？
  - 当一个对象不再使用需要回收时，由于另一个正在使用的对象持有它的引用，导致不能被及时回收仍保留在对内存中，这就产生了内存泄漏
  - 检查和分析：`内存泄漏发生在堆内存中`,需要借助MAT、LeakCannary等工具来进行监测分析
  - 常见内存泄漏
    - 情形一：单例造成：
    `主要是上下文context的引用（单例的静态特性使单例的生命周期与应用程序生命周期一样长，这就导致该单例一直持有使用它的对象的实例对象而不能被回收，出现内存泄漏）`
         - 解决办法：使用`application`的`context`代替具体的Context
    - 情形二：非静态内部类创建静态实例对象:
    ```非静态内部类、匿名内部类都会持有外部类的引用，而静态内部类不会```
         - 解决办法：将该非静态内部类设为静态内部类或者将该内部类抽取出来封装成一个单例对象，如需用到context则使用Application的context
    - 情形三：Handler造成
    ```当Activity关闭时，还存在未处理的消息（Message）,而每条消息都会持有Handler的引用，而Handler又持有Activity的引用，这就导致了Activity不能被回收出现内存泄漏。```
         - 解决办法
            - 方法1、将Handler独立出来
            - 方法2、使用静态内部类
            - 方法3、在关闭页面之前停止该页面线程（Thread）并移除所有消息
    - 情形四：线程造成：
       ```AsyncTask、Runnable都使用了匿名内部类，都隐式持有activity的引用导致内存泄漏```
             - 解决办法：将AsyncTask和Runnable类独立出来或者使用静态内部类
    - 情形五：资源未关闭
    ```BraodcastReceiver、ContentObserver、File、Cursor、Stream、Bitmap等资源```
    
        - 解决办法：使用完毕及时关闭或注销
             - 动态注册广播，需要销毁前解绑`register`、`register`
             - Cursor，I/O(stream)操作、File文件使用完毕及时关闭
             - Bitmap对象不在使用时调用recycle()释放内存。2.3以后的bitmap应该是不需要手动recycle
    - 情形六：集合造成
        - 解决办法：退出前clear掉集合的数据，并置空
    - 情形七：使用ListView时造成的内存泄漏
    ```Adapter中，没有使用缓存的convertView```
        - 解决办法：在构造Adapter中使用缓存的convertView。
    - 情形八：WebView造成的泄露
        - 解决办法：退出前调用destroy方法来销毁
### OOM原因？
   ```out of memory```内存耗尽了（申请的内存超出了JVM的可分配内存）
  
### ANR原因？
   ```application not response``` 应用程序无响应（超时）
   - Activity 5s
   - BroadcastReceiver 10s
   - Service 20s
   
   - 场景：
     - UI线程进行网络请求，数据库操作，File文件操作——总结为：在UI线程中进行耗时操作
     - UI线程等待子线程释放某个锁，导致无法处理用户的请求的输入
     - 耗时操作的动画需要大量的计算，可能导致CPU负载过重
   - 定位：
     - 查看logcat日志或手机目录下文件`/data/anr/traces.txt`

## 面试题
- Android常用布局
  - LinearLayout 线性布局
  - RelativeLayout 相对布局
  - FrameLayout 帧布局
  - AbsoluteLayout 绝对布局
  - TableLayout 表格布局
  - ConstraintLayout 与RelativeLayout相似，更灵活
  
   ````前五种为通常所说的Android中五大布局````
  
- 说说Handler机制
  ```handler是Android中的消息处理机制（可以收发消息）也是UI更新机制```
   - Handler、Looper、Message、MessageQueen
- Handle与Looper数量对应关系
  - 一个线程可以有多个Handler最多只能有一个Looper，当一个线程已经拥有Looper了再次调用Looper.prepare()方法，会抛出异常信息``Only one Looper may be created per thread``
- Handler可能引起内存泄漏吗?
  - handle可能引起内存泄漏
  - 当界面关闭时还有未处理消息的时候，每条消息都持有Handler的引用，而Handler又持有activity的引用，导致activity不能被回收出现内存泄漏。
- 子线程能更新UI吗？为什么？如果在子线程中更新UI会出现什么问题？
- ANR是什么？如何避免？
- 谈谈对OOM的理解
- synchronized与volatile的区别
    - 1、volatile只能作用于变量，使用范围较小。synchronized可以用在变量、方法、类、同步代码块等，使用范围比较广。 
   - 2、volatile只能保证可见性和有序性，不能保证原子性。而可见性、有序性、原子性synchronized都可以包证。 
   - 3、volatile不会造成线程阻塞。synchronized可能会造成线程阻塞
   
   本文来自 [孤客无依 的CSDN 博客](https://blog.csdn.net/huideveloper/article/details/80632111?utm_source=copy)
- sleep与wait方法区别
```
对于sleep()方法，该方法是属于Thread类中的。而wait()方法，则是属于Object类中的。
sleep()方法导致了程序暂停执行指定的时间，让出cpu该其他线程，但是他的监控状态依然保持者，当指定的时间到了又会自动恢复运行状态。

在调用sleep()方法的过程中，线程不会释放对象锁。

而当调用wait()方法的时候，线程会放弃对象锁，进入等待此对象的等待锁定池，只有针对此对象调用notify()方法后本线程才进入对象锁定池准备

获取对象锁进入运行状态
```
- ThreadLocal原理
- 你所知道的设计模式
- 范型原理
- 接口和抽象类的区别？应用场景
- 静态属性、静态方法是否可以被继承？是否可以被重写？为什么？
- FrameWork的工作方式和原理，Activity是如何生成一个view的，机制是什么？
- View的渲染
- 描述下service的生命周期，如何和Activity绑定，怎么在service中启动自己对应的service?
- ListView中图标混乱是怎么产生的？
- ListView加载一万条以上数据，如何优化？
- 一张Bitmap所在内存和内存占用的计算？
- 如果需要在应用在，同时加载50张高清图片，如何实现？
- RecycleView与ListView的区别？为什么RecycleView优于ListView?
  - ```The RecyclerView widget is a more advanced and flexible version of ListView.```RecycleView（v7包下）相比来说更高级、更灵活一些
- 自定义View方法
- 自定义View如何自定义宽高
- touch事件分发机制
- dispatchTouchEvent与onDraw区别
- service的生命周期和启动方式
- 8.0后对Service的新特性
- service的onStartCommand方法在什么情况下不会被调用
- 线程间通信方式，进程间通信方式？
[参考资料](https://blog.csdn.net/ls5718/article/details/51878770)
  - java中线程间通信
    - 共享变量
    - wait/notify机制
    - Lock/Condition机制
    - 管道
   - Android中线程间通信
     - Handle机制
     - runOnUiThread方法
     - View.post(Runnable r)、postDelay
     - AsyncTask
     - EventBus实现线程间的通信
     - Broadcast
  - java进程间通信
    - 管道(Pipe)
    - 命名管道(named pipe)
    - 信号（Signal）
    - 消息队列
    - 共享内存
    - 内存映射
    - 信号量
    - 套接字（socket）
  - Android中进程通信[参考](https://www.cnblogs.com/lizhengxian/p/5075635.html)
    - Bundle/Intent传递数据
    - 文件共享
    - AIDL
    - ContentProvider
    - Socket 
- 消息推送使用第三方和自己实现有什么不同
- webView优化
- 如何对Android进行性能分析？
- 什么情况会导致内存泄漏？
- 内存泄漏如何分析，怎样避免？

----------------------------------------

- 编写一个判别质数的函数，是质数函数返回值1，否则返回0
- 编写函数求两个数的最大公约数和最小公倍数
- 将一个正整数分解质因子 `如：90=2*3*3*5`

- 1元钱纸币，兑换成1分，2分，5分的硬币，要求每种至少1枚，打印出有多少种兑换方法并打出具体方式

- n个人围成一圈，从第一个开始报数（1-3），凡是报到3的人退出圈子，问最后留下的是原来的几号
- 打印出杨辉三角形（要求打印10行）
```
       1
      1 1
    1  2 1
  1  3  3 1
 1 4  6  4 1
1 5 10 10 5 1
```


- 打印出三角形图案
```
    #
   ###
  #####
 #######
#########
```

- 一个正数有可能被表示为n（n>=2）个连续正整数之和；
```
如：15=1+2+3+4+5
15=4+5+6
15=7+8
```
```
 要求：输入采用命令行方式，打印出符合条件的序列，每个序列从最小正整数开始，每行一个序列；若有多个序列则按每个序列最小数排（从小到大）
      序列不允许重复，序列内用一个空格分隔，没有符合的输出NONE
```

- 两队比赛。A队a,b,c三人，B对x,y,z三人；以抽签决定比赛名单 ，a说他不和x比，c说他不和x、z比，编写程序找出三队赛手的名单




-----------------

### 结束

```
通常面试到最后面试官不管是出于礼貌性或者真正想知道，基本上都会询问面试者：你有什么想问我的吗？
很多人都会说没有（有可能是真没有了，我想这种情况真的很少，说没有的人猜测可能原因：1、面试不理想直接放弃了，2、不好意思问 3、不知道该问点什么）

```
```python

询问面试官问题，好处多多：
1、不管最后结果怎样，都对应聘公司更深入的了解，便于以后抉择
2、增加更多的交流机会
3、自身情况对比
4、还有不少好处后面补充吧，总之问他几个就对了，千万别再说没有了
```

- 这里放几个建议问题（仅供参考啊）
  - 1、公司目前的业务是做什么的？（大体了解）
  - 2、公司目前的项目面临的最大困难是什么？（如果面试成功，这很有可能是你要面对的了；还有就是很多面试官也会问你同样的问题，哈哈....相信很多人都被问过吧）
  - 3、如果能进入公司，主要工作是做什么？（了解工作内容看是否能够胜任吧）
  - 4、通过这次面试，您觉得我还有哪些不足？能给点建议和指导吗？（成长）
  - 5、对于离职率大的公司，可以直接问：您觉得离职率这么大的主要原因是什么呢？（更一步了解）