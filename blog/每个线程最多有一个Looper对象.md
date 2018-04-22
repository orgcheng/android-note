

## 一、每个线程最多有一个Looper对象 ##

Looper的构造方法是private，只能调用prepare()方法来初始化
	
	// Looper.java文件
    // sThreadLocal.get() will return null unless you've called prepare().
    static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
    private static Looper sMainLooper;  // guarded by Looper.class
	
	private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);
        mThread = Thread.currentThread();
    }

	private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));
    }


下面看下Thread类的字段
	
	// Thread.java文件
    /* ThreadLocal values pertaining to this thread. This map is maintained by the ThreadLocal class. */
    ThreadLocal.ThreadLocalMap threadLocals = null;
	

下面看下ThreadLocal类的相关方法

	// ThreadLocal.java文件
	public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }

	// 返回ThreadLocal关联的Map
    ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }

	private T setInitialValue() {
        T value = initialValue();
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
        return value;
    }
	
	void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);
    }

<br/>

从上面的代码流程看，Looper通过静态字段sThreadLocal，来保存当前线程的Looper对象，通过私有化Looper的构造方法，在prepare方法中构造并保证线程只能有一个Looper对象

因为ThreadLocal.ThreadLocalMap类和Thread的ThreadLocal.ThreadLocalMap成员变量都是被default修饰，只能被相同包下面的类访问，所以只能通过ThreadLocal对象操作、或者反射操作



## 二、下面看一下ThreadLoal.ThreadLocalMap和ThreadLoal.ThreadLocalMap.Entry ##

每个线程都有threadLocals字段，用户通过操作ThreadLocal对象就可以访问当前线程的threadLocals变量，ThreadLocal就像是代理，代理当前线程管理它的ThreadLocal.ThreadLocalMap变量。


    /**
     * ThreadLocalMap is a customized hash map suitable only for
     * maintaining thread local values. No operations are exported
     * outside of the ThreadLocal class. The class is package private to
     * allow declaration of fields in class Thread.  To help deal with
     * very large and long-lived usages, the hash table entries use
     * WeakReferences for keys. However, since reference queues are not
     * used, stale entries are guaranteed to be removed only when
     * the table starts running out of space.
     */
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
	｝

为了帮助处理长期存活的大对象的使用，`Entry`使用了弱引用作为key。另外，因为没有引用队列，当`ThreadLocalMap`的`Entry[] table`空间不够的时候，才会清理过期的`Entry对象`，所谓过期的实体，就是`entry.get()==null`。

`ThreadLocalMap`提供了一些方法，方便对`Entry`列表进行增加和移除，还清除过期`Entry`   

其中`ThreadLocalMap.getEntry`和`ThreadLocalMap.set`方法极其自方法是关键 

<br/>