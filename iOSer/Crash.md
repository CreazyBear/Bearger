# Crash
#程序员/iOS/其它

1. Unrecoginzed Selector Crash

[Objective-C Runtime 运行时之三：方法与消息 | 南峰子的技术博客](https://southpeak.github.io/2014/11/03/objective-c-runtime-3/)


2. KVO Crash

出现原因：
* 不匹配的移除和添加关系。
* 观察者和被观察者释放的时候没有及时断开观察者关系。

解决方案：
重写NSObject的下面两个方法
```Objective-C
- (void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(void *)context;

- (void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath;

```

3. Container Crash

出现原因：
容器插入了 nil 就会导致 Crash
容器下标越界。

```Objective-C
- (ObjectType)objectAtIndex:(NSUInteger)index;

- (void)setObject:(ObjectType)anObject forKey:(id<NSCopying>)aKey;
- (void)setValue:(ObjectType)value forKey:(NSString *)key;
```

> 关于`setValue:forKey:`和`setObject:forKey:`的差异在`setValue:forKey:`的文档中写得很清楚  
> This method（setValue:forKey:） adds value and key to the dictionary using setObject:forKey:, unless value is nil in which case the method instead attempts to remove key using removeObjectForKey:.  



4. NSNotification Crash

出现原因：
在 iOS8 及以下的操作系统中添加的观察者一般需要在 dealloc 的时候做移除，如果开发者忘记移除，则在发送通知的时候会导致 Crash，而在 iOS9 上即使移忘记除也无所谓，猜想可能是 iOS9 之后系统将通知中心持有对象由 strong 变为了weak。

5. NSTimer leak

出现原因：
在使用 `+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti target:(id)aTarget selector:(SEL)aSelector userInfo:(nullable id)userInfo repeats:(BOOL)yesOrNo `创建定时任务的时候，target 一般都会持有 timer，timer又会持有 target 对象，在我们没有正确关闭定时器的时候，timer 会一直持有target 导致内存泄漏。


6. 野指针 Crash
多线程操作同一个属性时。
eg1：
在使用`lws_websocket`时需要定时执行`lws_service`方法。如下面的代码所示：
```Objective-C
            [_websocketServicesGuarder increase];
            lws_service(context, 0);
            [_websocketServicesGuarder decrease];
```
而当断开会话时，需要将`context`设置为NULL，因为`lws_websocket`是一个C库。
如果此时，A线程在发送socket请求（这是一个异步耗时操作），而在这时用户点击返回，由B线程执行清理现场操作。B线程执行了`context=NULL`,但这一变量A线程还在使用（lws_websocket内部使用了外部传参的引用），此时变量的内存已经被释放。

eg2:
我之前写的一个表情图片缓存的方法，最开始没有加锁。在线上会有非常偶现的Crash。目前不知道确切的原因 =。=，个人猜测是多个线程进入`[self addEmotionIcon:imageIcon withIconName:iconName];`里面。而向字典中添加重复元素时，会将上一个元素弹出。A线程先向字典里存图片，存到一半的时候，B线程也向字典中添加同一Key，然后把A的对象给干掉了。于是A挂了。
```Objective-c
-(void)addEmotionIcon:(UIImage*)image withIconName:(NSString*)iconName
{
    if (_emotionIcon && image && ![NSString isNilOrEmpty:iconName]) {
        /*不加锁的话，这里会偶现Crash，image为空。但是在复现Crash时,
			image的内容是可以通过Xcode查看到的~！
			*/	
        [_emotionIcon setObject:image forKey:iconName];
    }
}

-(UIImage*)getEmotionIconWithIconName:(NSString*)iconName
{
    UIImage* imageIcon;
    @synchronized(self) {
        imageIcon = [_emotionIcon objectForKey:iconName];
        if(!imageIcon)
        {
            imageIcon = [UIImage imageNamed:iconName];
            [self addEmotionIcon:imageIcon withIconName:iconName];
        }

    }
    return imageIcon;
    
}

```









