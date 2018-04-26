### 一、Bitmap的创建###

我们都是通过BitmapFactory来创建

	public static Bitmap decodeFile(String pathName, Options opts);
	public static Bitmap decodeByteArray(byte[] data, int offset, int length, Options opts);
	public static Bitmap decodeResource(Resources res, int id, Options opts);
	public static Bitmap decodeStream(InputStream is, Rect outPadding, Options opts);

上面这些方法需要传递`Options`参数，通过这个参数我们可以优化`Bitmap`的使用。

`Options`是`BitmapFactory`的`public`静态内部类,可以修改`Options`下面的字段值来控制如何加载`Bitmap`：

	inJustDecodeBounds
	outWidth
	outHeight
	inPreferredConfig
	inSampleSize

<br/>

**`Bitmap.Config`的枚举值，指定一个bitmap一个像素存储占用几个字节。**

> Config.ALPHA_8   每个像素占1个字节，只有A通道被编码
> 
> Config.RGB_565   每个像素占2个字节，只有RGB通道被编码，R占5个bit位，G占6个bit位，B占5个bit位
>
> Config.ARGB_8888 每个像素占4个字节, ARGB每个通道占8个bit位


<br/>

### 二、LruCache -- 内存缓存 ###

Android提供的工具类，framworks和v4包中都有提供，建议使用v4包下面的，framworkds貌似有问题：

	// trimToSize方法的参数为负数时，导致死循环
    public final void evictAll() {
        trimToSize(-1); // -1 will evict 0-sized elements
    }


<br/>

缓存的数据被保存在`LinkedHashMap`，所以该类的关键就是它了：

    map = new LinkedHashMap<K, V>(0, 0.75f, true);

<br/>

下面看看`LinkedHashMap`类的声明，它继承`HashMap`，重写了`HashMap`的添加和查询方法

	public class LinkedHashMap<K,V>	extends HashMap<K,V> implements Map<K,V>{
		...
	
		public V get(Object key) {...}

		void addEntry(int hash, K key, V value, int bucketIndex) {...}
	
		void createEntry(int hash, K key, V value, int bucketIndex) {...}
		
		void transfer(HashMapEntry[] newTable) {...}
	}

因此，想要理解`LinkedHashMap`，需要先理解`HashMap`，对于`HashMap`可以从它的`put`和`get`方法入手，`put`方法中会调用`addEntry`和`createEntry`方法，子类修改put的规则，需要重新这两个方法，`transfer`是扩容的时候调用的方法。

<br/>
 

`HashMap`通过hash和indexFor计算存放的位置，如果位置被占用，通过链表来扩展来避免冲突。

`LinkedHashMap`是`HashMap`的基础上，对`HashMapEntry`类添加额外字段，维护entry的双向链表。


花了一下午时间，屡清楚上`LinkedHashMap`和`HashMap`。

<br/>

### 三、DiskLruCache -- 磁盘缓存 ###

因为是第三方类，被google认证通过，但是使用的时候，应该包装一层，同时还可以对包装类添加个版本，便于后期维护。


<br>
---
**写这个文章的灵感来自[Bitmap高效缓存](http://www.imooc.com/learn/913)，可以直接看视频，慕课网的免费视频。**