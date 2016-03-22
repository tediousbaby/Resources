
###### ThreadLocal是什么?

ThreadLocal是一个关于创建线程局部变量的类。

通常情况下，我们创建的变量是可以被任何一个线程访问并修改的。而使用ThreadLocal创建的变量只能被当前线程访问，其他线程则无法访问和修改。

###### 常规用途

- 实现单个线程单例以及单个线程上下文信息存储，比如交易id等
- 实现线程安全，非线程安全的对象使用ThreadLocal之后就会变得线程安全，因为每个线程都会有一个对应的实例
- 承载一些线程相关的数据，避免在方法中来回传递参数

###### ThreadLocal的实例以及其值存放在哪里？

因为ThreadLocal实例实际上也是被其创建的类持有（更顶端应该是被线程持有）。而ThreadLocal的值其实也是被线程实例持有。它们都是位于堆上，只是通过一些技巧将可见性修改成了线程可见。

###### 会导致内存泄露么?

有网上讨论说ThreadLocal会导致内存泄露，原因如下

首先ThreadLocal实例被线程的ThreadLocalMap实例持有，也可以看成被线程持有，如果应用使用了线程池，那么之前的线程实例处理完之后出于复用的目的依然存活。所以ThreadLocal设定的值被持有导致内存泄露。

上面的逻辑是清晰的，可是ThreadLocal并不会产生内存泄露，因为ThreadLocalMap做选择key的时候，并不是直接选择ThreadLocal实例，而是ThreadLocalMap实例的弱引用。

```
static class ThreadLocalMap {

    /**
    * The entries in this hash map extend WeakReference, using
    * its main ref field as the key (which is always a
    * ThreadLocal object).  Note that null keys (i.e. entry.get()
    * == null) mean that the key is no longer referenced, so the
    * entry can be expunged from table.  Such entries are referred to
    * as "stale entries" in the code that follows.
    */
    static class Entry extends WeakReference<ThreadLocal<?>> {
        /** The value associated with this ThreadLocal. */
        Object value;

        Entry(ThreadLocal<?> k, Object v) {
            super(k);
            value = v;
        }
    }
}
```
