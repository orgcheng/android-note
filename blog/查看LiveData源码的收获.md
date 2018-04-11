### 一、什么是DataLive ###
    LiveData is a data holder class that can be observed within a given lifecycle.

    This means that an Observer can be added in a pair with a LifecycleOwner, and
	this observer will be notified about modifications of the wrapped data only if
	the paired LifecycleOwner is in active state.

	类的文档中已经声明， LiveData保存了一个数据，关联Observer和LifecycleOwner后，当LiveData
	中的数据改变后，决定什么时候回调Observer的方法。

`LiveData`中的成员方法`setValue`和`postValue`，都是`protected`修饰的，导致`LiveData`中的数据不能被修改，所以又提供了子类`MutableLiveData`，这个子类仅仅是修改上面两个方法的修饰符为`public`。

`LiveData`的另个一个子类是`MediatorLiveData`

    public class MediatorLiveData<T> extends MutableLiveData<T> {
		private SafeIterableMap<LiveData<?>, Source<?>> mSources = new SafeIterableMap<>();

		...

		public <S> void addSource(@NonNull LiveData<S> source, @NonNull Observer<S> onChanged) {
			...
		｝
	｝
调用`addSource`方法，它可以包含多个`LiveData`对象，此时`LiveData`对象关联的`LifecycleOwner`一直是`active`状态，这个`LifecycleOwner`对象是`LiveData`的静态变量，全局唯一。

当`MediatorLiveData`处于`active`，它包含的多个`LiveData`对象关联对应的`Observer`对象；

当`MediatorLiveData`处于`inactive`，它包含的多个`LiveData`对象取消关联对应的`Observer`对象；

<br/>
### 二、`DataLive`的`postValue`方法 ###

	private static final Object NOT_SET = new Object();
	private volatile Object mPendingData = NOT_SET;		
	private volatile Object mData = NOT_SET;

	protected void postValue(T value) {
        boolean postTask;
        synchronized (mDataLock) {
            postTask = mPendingData == NOT_SET;
            mPendingData = value;
        }
        if (!postTask) {
            return;
        }
		// 主线程中执行mPostValueRunnable
        ArchTaskExecutor.getInstance().postToMainThread(mPostValueRunnable);
    }

	private final Runnable mPostValueRunnable = new Runnable() {
        @Override
        public void run() {
            Object newValue;
            synchronized (mDataLock) {
                newValue = mPendingData;
                mPendingData = NOT_SET;
            }
            setValue((T) newValue); //如果setValue很耗时，比如修改数据库
        }
    };

当N个子线程并发调用`postValue`方法时，`mPostValueRunnable`的执行次数<N。

如果setValue很耗时，比如修改数据库，那么上面的代码可以大大的优化性能。

比如微信的未读消息更新时，通知桌面程序，桌面然后更新角标的显示。

当微信的未读消息频繁变化时，这种场景就可以复用上面的逻辑。