# 管理内存
todo：分析内存使用、Systrace、Traceview、StrictMode

Memory Leaks：通常由于在全局成员变量中持有对象引用而导致

策略：释放可能hold住的内存

1. 用IntentService代替Service，它会在在处理完扔给它的intent任务之后尽快结束自己。
2. UI隐藏时释放内存:实现Activtiy类里面的onTrimMemory())回调方法。监听到TRIM_MEMORY_UI_HIDDEN,释放那些仅仅被你的UI使用的资源。内存紧张时释放部分内存.API<14使用onLowMemory回调来进行兼容。
3. 通过调用getMemoryClass())来获取可用heap大小，在manifest的application标签下添加largeHeap=true的属性来声明一个更大的heap空间，通过getLargeMemoryClass()来获取到heap size

代码优化

* 字符串连接用StringBuilder，并发下用StringBuffer，指定默认长度
* 使用SoftReference、WeakReference代替强应用
* 使用SoftReference、WeakReference相对正常的强应用 
* ArrayList根据index取值更快，LinkedList更占内存、随机插入删除更快速、扩容效率更高。
* hash系列数据结构查询速度更优，ArrayList存储有序元素，HashMap为键值对数据结构，LinkedHashMap可以记住加入次序的hashMap，HashSet不允许重复元素。
* WeakHashMap中元素可在适当时候被系统垃圾回收器自动回收，所以适合在内存紧张型中使用。
* ConcurrentHashMap为细分锁，锁粒度更小，并发性能更优。Collections.synchronizedMap为对象锁，自己添加函数进行锁控制更方便。
* SparseArray、SparseBooleanArray、SparseIntArray 为key为int情况的特殊处理，采用二分查找及简单的数组存储，加上不需要泛型转换的开销，相对Map来说性能更优。根据可能的容量设置初始值。
* 不在生命周期等对时间敏感的函数中执行耗时操作，用ScheduledExecutorService，handler.postDelayed，handler.postAtTime，handler.sendMessageDelayed，View.postDelayed，AlarmManager定时等延迟操作
* 避免内部的Getters/Setters，把内部类需要访问的域和方法声明为包级访问
* 使用增强型for循环写法
