# Lock

# 方式一：线程锁协议NSLocking<Protocol>

实现该协议的类：
* NSCondition
* NSConditionLock
* NSLock
* NSManagedObjectContext
* NSOpenGLContext
* NSPersistentStoreCoordinator
* NSRecursiveLock

其中用来加锁的：

* NSLock
* NSCondition
* NSConditionLock
* NSRecursiveLock

## NSLock
文档上写得很清楚了

## NSCondition

Api，看下大概了解到，NSCondition比NSLock多了等待和唤醒的接口。当需要对单个变量进行多线程操作时，可以使用这个
```
Waiting for the Lock
- wait
Blocks the current thread until the condition is signaled.
- waitUntilDate:
Blocks the current thread until the condition is signaled or the specified time limit is reached.
Signaling Waiting Threads
- signal
Signals the condition, waking up one thread waiting on it.
- broadcast
Signals the condition, waking up all threads waiting on it.

```

## NSConditionLock

NSConditionLock 和 NSLock 类似，都遵循 NSLocking 协议，方法都类似，只是多了一个 condition 属性，以及每个操作都多了一个关于 condition 属性的方法，例如 tryLock，tryLockWhenCondition:，NSConditionLock 可以称为条件锁，只有 condition 参数与初始化时候的 condition 相等，lock 才能正确进行加锁操作。而 unlockWithCondition: 并不是当 Condition 符合条件时才解锁，而是解锁之后，修改 Condition 的值

```objc
//主线程中
    NSConditionLock *lock = [[NSConditionLock alloc] initWithCondition:0];

    //线程1
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        [lock lockWhenCondition:1];
        NSLog(@"线程1");
        sleep(2);
        [lock unlock];
    });

    //线程2
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        sleep(1);//以保证让线程2的代码后执行
        if ([lock tryLockWhenCondition:0]) {
            NSLog(@"线程2");
            [lock unlockWithCondition:2];
            NSLog(@"线程2解锁成功");
        } else {
            NSLog(@"线程2尝试加锁失败");
        }
    });

    //线程3
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        sleep(2);//以保证让线程2的代码后执行
        if ([lock tryLockWhenCondition:2]) {
            NSLog(@"线程3");
            [lock unlock];
            NSLog(@"线程3解锁成功");
        } else {
            NSLog(@"线程3尝试加锁失败");
        }
    });

    //线程4
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        sleep(3);//以保证让线程2的代码后执行
        if ([lock tryLockWhenCondition:2]) {
            NSLog(@"线程4");
            [lock unlockWithCondition:1];    
            NSLog(@"线程4解锁成功");
        } else {
            NSLog(@"线程4尝试加锁失败");
        }
    });

2016-08-19 13:51:15.353 ThreadLockControlDemo[1614:110697] 线程2
2016-08-19 13:51:15.354 ThreadLockControlDemo[1614:110697] 线程2解锁成功
2016-08-19 13:51:16.353 ThreadLockControlDemo[1614:110689] 线程3
2016-08-19 13:51:16.353 ThreadLockControlDemo[1614:110689] 线程3解锁成功
2016-08-19 13:51:17.354 ThreadLockControlDemo[1614:110884] 线程4
2016-08-19 13:51:17.355 ThreadLockControlDemo[1614:110884] 线程4解锁成功
2016-08-19 13:51:17.355 ThreadLockControlDemo[1614:110884] 线程1

```

## NSRecursiveLock

NSRecursiveLock 是递归锁，他和 NSLock 的区别在于，NSRecursiveLock 可以在一个线程中重复加锁（反正单线程内任务是按顺序执行的，不会出现资源竞争问题），NSRecursiveLock 会记录上锁和解锁的次数，当二者平衡的时候，才会释放锁，其它线程才可以上锁成功。一般在递归加锁的时候用到吧~！

```objc
NSRecursiveLock *lock = [[NSRecursiveLock alloc] init];

    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{

        static void (^RecursiveBlock)(int);
        RecursiveBlock = ^(int value) {
            [lock lock];
            if (value > 0) {
                NSLog(@"value:%d", value);
                RecursiveBlock(value - 1);
            }
            [lock unlock];
        };
        RecursiveBlock(2);
    });

2016-08-19 14:43:12.327 ThreadLockControlDemo[1878:145003] value:2
2016-08-19 14:43:12.327 ThreadLockControlDemo[1878:145003] value:1

```


- - - -

# 方式二：@synchronized代码块

```objc
@synchronized(self.protocolManagerDictionary) {
    //CODE
}

```


- - - -

# 方式三：GCD线程流程控制

## 条件信号量dispatch_semaphore_t

```objc
- (void)getIamgeName:(NSMutableArray *)imageNames{
    
    NSString *imageName;
    /**
     *  semaphore：等待信号
     DISPATCH_TIME_FOREVER：等待时间
     wait之后信号量-1，为0
     */
    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
    {
		//locked code
	   }    

    /**
     *  发送一个信号通知，这时候信号量+1，为1
     */
    dispatch_semaphore_signal(semaphore);
}
```

```objc
+(void)loadJSFilesFromServer:(NSArray*)fileNames withCompleteBlock:(void (^)())block
{
    
    dispatch_semaphore_t semaphore = dispatch_semaphore_create(0);
    dispatch_group_t group = dispatch_group_create();
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    
    [fileNames enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        
        dispatch_group_async(group, queue, ^{
            
            NSString * urlStr = [NSString stringWithFormat:@"%@/%@.js",autoUIServerPath,obj];
            [TNAutoUITemplateLoader downloadSourceFileWithUrlStr:urlStr
                                                     withTimeOut:30
                                              withRespoonseBlock:^(NSURL *location, NSURLResponse *response, NSError *error) {
                                                  dispatch_semaphore_signal(semaphore);
                                             }];
        });
        
    }];
    
    dispatch_group_notify(group, queue, ^{
        for (int i = 0; i<fileNames.count; i++)
        {
            dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
        }
        !block?:block();
    });

}
```

## dispatch_barrier_async/dispatch_barrier_sync
栅栏任务：对于并发的GCD任务，将指定的任务以串行方式进行执行。
```
        dispatch_barrier_async(<#queue#>, ^{
             [self getIamgeName:imageNames];
        });
```

- - - -

# 方式四：互斥锁POSIX
这是底层提供的方法，就好比线程有pthread，NSThread，GCD一样。
```objc
// 初始化
    int pthread_cond_init (pthread_cond_t *cond, pthread_condattr_t *attr);

    // 等待（会阻塞）
    int pthread_cond_wait (pthread_cond_t *cond, pthread_mutex_t *mut);

    // 定时等待
    int pthread_cond_timedwait (pthread_cond_t *cond, pthread_mutex_t *mut, const struct timespec *abstime);

    // 唤醒
    int pthread_cond_signal (pthread_cond_t *cond);

    // 广播唤醒
    int pthread_cond_broadcast (pthread_cond_t *cond);

    // 销毁
    int pthread_cond_destroy (pthread_cond_t *cond);
```

eg:
```objc
#import <pthread.h>
@interface MYPOSIXViewController ()
{
    pthread_mutex_t mutex;  //声明pthread_mutex_t的结构
}
@end

@implementation MYPOSIXViewController
- (void)dealloc{
    pthread_mutex_destroy(&mutex);  //释放该锁的数据结构
}
- (void)viewDidLoad {
    [super viewDidLoad];
    //初始化
    pthread_mutex_init(&mutex, NULL);
}

- (void)getIamgeName:(NSMutableArray *)imageNames{
    NSString *imageName;
    //加锁
    pthread_mutex_lock(&mutex);
    {
        //lOCKED code
    }
    //解锁
    pthread_mutex_unlock(&mutex);
}

```

# 方式五：线程安全的计数
在写websocket底层的时候用到过，同事说加锁不起作用，于是用了这个
```objc
#import <libkern/OSAtomic.h>
/// a thread safe incrementing counter.
@interface _YYTextSentinel : NSObject
/// Returns the current value of the counter.
@property (atomic, readonly) int32_t value;
/// Increase the value atomically. @return The new value.
- (int32_t)increase;
@end

@implementation _YYTextSentinel {
    int32_t _value;
}
- (int32_t)value {
    return _value;
}
- (int32_t)increase {
    return OSAtomicIncrement32(&_value);
}
@end
```

---
# 总结

虽然都是为了解决多线程资源竞争的问题，但都各有特点吧。

方式一：线程锁协议NSLocking<Protocol>这个算是官方的吧。几个实现类都有各自的特点

方式二：@synchronized代码块，这个其实挺方便的，可并不能解决所有场景，有时还是无法达到线程安全

方式三：GCD线程流程控制 更多的是控制线程，而不是控制资源访问

方式四：互斥锁POSIX使用了UNIX底层API，在日常开发中感觉没有必要。

方式五：线程安全的计数 。呃，当上面加锁都不起作用的时候，可以试一下。

在选择锁的时候，需要注意自己需要加锁的资源是变量还是代码段。资源可能在多个地方被用到，这时需要类似信号量的加锁方式。