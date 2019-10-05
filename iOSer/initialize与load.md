# initialize与load

# 一、initialize
**Apple Doc**

>Initializes the class before it receives its first message.

**Discussion**

>The runtime sends initialize to each class in a program just before the class, or any class that inherits from it, is sent its first message from within the program. Superclasses receive this message before their subclasses.
>
>The runtime sends the initialize message to classes in a thread-safe manner. That is, initialize is run by the first thread to send a message to a class, and any other thread that tries to send a message to that class will block until initialize completes.
>
>The superclass implementation may be called multiple times if subclasses do not implement initialize—the runtime will call the inherited implementation—or if subclasses explicitly call [super initialize]. If you want to protect yourself from being run multiple times, you can structure your implementation along these lines:

```Objective-C
+ (void)initialize {
  if (self == [ClassName self]) {
    // ... do the initialization ...
  }
}
```

>Because initialize is called in a blocking manner, it’s important to limit method implementations to the minimum amount of work necessary possible. Specifically, any code that takes locks that might be required by other classes in their initialize methods is liable to lead to deadlocks. Therefore, you should not rely on initialize for complex initialization, and should instead limit it to straightforward, class local initialization.

**Special Considerations**

>-initialize is invoked only once per class.- If you want to perform independent initialization for the class and for categories of the class, you should implement load methods.

总结：
1. initialize在使用（发送第一个消息）之前被调用
2. initialize可能被多次调用
	1. 父类实现initialize方法，但子类没有实现
	2. 子类中主动调用`[super initialize];`
3. initialize是线程安全的，当initialize在执行期间，其它方法调用被阻塞
4. initialize不提供category支持，当在category中添加相应方法时，原类的initialize被重写，即使category没有被import。
5. 不用显示调用`[super initialize];`


# 二、 load

**Apple Doc**

> Invoked whenever a class or category is added to the Objective-C runtime; implement this method to perform class-specific behavior upon loading.
>
>Discussion
>
>The load message is sent to classes and categories that are both dynamically loaded and statically linked, but only if the newly loaded class or category implements a method that can respond.
>
>The order of initialization is as follows:
>
>1. All initializers in any framework you link to.
>
>2. All +load methods in your image.
>
>3. All C++ static initializers and C/C++ __attribute__(constructor) functions in your image.
>
>4. All initializers in frameworks that link to you.
>
>In addition:
>
>* A class’s +load method is called after all of its superclasses’ +load methods.
>
>* A category +load method is called after the class’s own +load method.
>
>In a custom implementation of load you can therefore safely message other unrelated classes from the same image, but any load methods implemented by those classes may not have run yet.
>
>Important
>Custom implementations of the load method for Swift classes bridged to Objective-C are not called automatically.

总结：
1. 如果你实现了 + load 方法，那么当类被加载时它会自动被调用。这个调用非常早。如果你实现了一个应用或框架的 + load，并且你的应用链接到这个框架上了，那么 ＋ load 会在 main() 函数之前被调用。如果你在一个可加载的 bundle 中实现了 + load，那么它会在 bundle 加载的过程中被调用。
2. 调用优先级：父类 > 子类 > 分类
3. 不用显示调用`[super load]`
4. 只被会调用一次
5. 不会被分类覆盖


# 三、有啥用呢？
装X 😎

eg：SDWebImage----	`SDWebImageDownloader.m`

SDWebImage中的网络loading效果是通过别外一个库来实现监听的

```objc
+ (void)initialize {
    // Bind SDNetworkActivityIndicator if available (download it here: http://github.com/rs/SDNetworkActivityIndicator )
    // To use it, just add #import "SDNetworkActivityIndicator.h" in addition to the SDWebImage import
    if (NSClassFromString(@"SDNetworkActivityIndicator")) {

#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-performSelector-leaks"
        id activityIndicator = [NSClassFromString(@"SDNetworkActivityIndicator") performSelector:NSSelectorFromString(@"sharedActivityIndicator")];
#pragma clang diagnostic pop

        // Remove observer in case it was previously added.
        [[NSNotificationCenter defaultCenter] removeObserver:activityIndicator name:SDWebImageDownloadStartNotification object:nil];
        [[NSNotificationCenter defaultCenter] removeObserver:activityIndicator name:SDWebImageDownloadStopNotification object:nil];

        [[NSNotificationCenter defaultCenter] addObserver:activityIndicator
                                                 selector:NSSelectorFromString(@"startActivity")
                                                     name:SDWebImageDownloadStartNotification object:nil];
        [[NSNotificationCenter defaultCenter] addObserver:activityIndicator
                                                 selector:NSSelectorFromString(@"stopActivity")
                                                     name:SDWebImageDownloadStopNotification object:nil];
    }
}
```


eg：替换方法，进行性能分析，打点数据等
```Objective-C
+ (void)load
{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        [self tn_swizzleInstanceSelector:@selector(viewDidLoad) withSelector:@selector(tna_viewDidLoad)];
        [self tn_swizzleInstanceSelector:@selector(viewWillAppear:) withSelector:@selector(tna_viewWillAppear:)];
        [self tn_swizzleInstanceSelector:@selector(viewDidAppear:) withSelector:@selector(tna_viewDidAppear:)];
    });
}
```

eg：FLEX注册模拟器按键监听
```Objective-C
+ (void)load
{
    dispatch_async(dispatch_get_main_queue(), ^{
        [[[self class] sharedManager] registerDefaultSimulatorShortcuts];
    });
}

```

其它的自行搜索热门的github库