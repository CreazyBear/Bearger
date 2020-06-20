# block野指针
#程序员/WTF

一般而言block的循环引用比较多，而野指针其实很少。
不过场景还是有的


下面这个场景可是说是少见的，或者说是`arrayWithObjects `的Bug吧。这个方法只深拷贝了第一个元素，而后面都只是浅拷贝。所以当getBlockArray结束时，block被释放了。这也就野指针了。
```
#import "ApplicationObject.h"

@implementation ApplicationObject

- (instancetype)init
{
    self = [super init];
    if (self) {
        [self test];
    }
    return self;
}

-(NSArray*) getBlockArray
{
    int num = 916;
    return [NSArray arrayWithObjects:
            ^{ NSLog(@"this is block 0:%i", num); },
            ^{ NSLog(@"this is block 1:%i", num); },
            ^{ NSLog(@"this is block 2:%i", num); },
            nil];
}

- (void)test
{
    NSArray*  blockArr = [self getBlockArray];
    
    void (^blockObject)(void);
    
    for(blockObject in blockArr){
        blockObject();
    }
}
@end

```



当面试的时候被问到这个问题，当然考察的不是上面的那种特殊情况了。应该是在使用block时为了防止循环引用，而使用的Weak操作。而weak之后的变量在block中没有进行strong操作，而使得weak变量在block外部被释放了。而block在使用时就直接野指针了。
