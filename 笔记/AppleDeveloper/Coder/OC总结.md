# OC总结

从Android转iOS想想也快两年了。要说当初为何要转？只能说年少无知。不过，影响还好，毕竟目前还是原生开发是主力，所以iOS和Android 1：1。后面可能会向大前端发展，但那个就是另外一件事了。只是国内还是Android的岗位相对来说多一点。智能硬件和IOT的时代下，开放的系统还是很占优势的。

入坑二年多，一直在忙着挖坑和爬坑。还没有抽出时间来对OC这门语言进行总结。最近想想，发现很多基础的东西渐渐淡忘了。工作中更多的是项目结构、产品需求、数据云云。对OC这门语言的探究已经很少了。

正好这次国庆，在家躺尸，无所可做（本来打算学习一下Swfit UI，但是Mac OS迟迟不更新）。所以，就重新回顾一下这门语言，巩固一下基础。算是混战之下的中场休息了。因为，个人觉得下半场，没OC啥事了。因为现在的UI开发太低效了，对比React和Fluter，还有Swift UI。这个后面等学习完Swift UI可以再聊，先回归正题。

所谓总结，不过是把之前看过的经典书再看一次。顺便把一些知识点，和自己实践中的认知记录下来。毕竟，当时在读书的时候，很多东西只是一知半解。

## 先从 OC 52 开始吧 

当然，第一本OC的书并不是这本。而且是OC语法书《Objective-C基础教程高清中文版（第2版）》。不过，时间精力有限，先把这个跳过吧（估计以后也不会回顾的）。OC52可以说很经典了，不过当时读的时候还是很蒙逼态的。

### 第一条：OC的起源

1. 消息结构、运行时
2. C的超集
3. 创建的对象所在的位置：堆、栈、全局\静态存储区、常量区

这条更多的是强调概念。上面的这一点，其实其实要吧展开很多。好比宪法是一切其它法律的基础样。第一点后面会其它关联点，第二点，了解一下就好了。关于第三点需要强调一下了。之前在学习C++的时候，有道题很经典。

```c++
//main.cpp 
int a = 0; //全局初始化区 
char *p1; //全局未初始化区 
main() 
{ 
    int b;// 栈 
    char s[] = "abc"; //"abc"在常量区，s在栈上
    char *p2; //栈 
    char *p3 = "123456"; //字符串"123456\0"在常量区，p3指针在栈上
    static int c =0； //全局（静态）初始化区 

    //分配得来得10和20字节的区域就在堆区。 
    p1 = (char *)malloc(10); 
    p2 = (char *)malloc(20); 
    
    //"123456\0"放在常量区，编译器可能会将它与p3所指向的"123456"优化成一个地方。
    strcpy(p1, "123456"); 
}
```

### 第二条：类的头文件中尽量少引入其它文件

这条规则的目的就是降低代码相互之间的依赖复杂度，并提高编辑时间。

1. 循环引用

    作者强调虽然 #import 可以避免循环引用，但编译失败。虽然已经import但依旧无法解析类型。但通过@class的方式就可以解决。

2. xcode点击类型跳转成功，但在.m文件中无法调用方法

    这个其实就是@class导致的。在一个简单的在三层嵌套关系中。`Dome1 import Demo2; Demo2 @class Demo3; ` 所以，在Demo1的.h文件中可直接定义Demo3的类型。但在.m文件中，就无法使用类型了。因为Demo1的.m文件中并不知晓Demo3的具体实现。所以在使用@class的时候，各.m文件需要各自import。

3. 将协议放在分类中。

    作者强调尽可能少的引入头文件，而且对于协议，是不可不引入的。而有的协议又和关系的类相互绑定，将协议单独拆分出来，反而使协议的理解变得复杂（类似UITableViewDelegate就和tableview绑定在一起）。针对这种情况，作者是推荐将协议的实现放在分类中。从分类的使用场景上来说，这样做的确要优雅很多。但分类也有分类的问题，后面再讲。

4. 我偏要放在头文件中

    作者强调的只是尽量少。但没说不能放。其实一些场景下就是要放在头文件中的。例如使用一个单例Store来管理对应的Model。所有对model的操作都必须经过store的接口。在使用到Store的时候，必然需要知晓model的详细信息。如果只在store中@class，那使用者在import store的时候都需要再import一次model。再比如埋点功能，埋点的定义被放在一个单独的头文件中，而埋点的实现在别一个类中。虽然埋点的实现类并不对埋点有任何的依赖。但我还是习惯将埋点定义的头文件放在埋点实现类的.h文件中。仅仅是因为使用起来方便。

5. 头文件引入之前的清理

    随着项目的迭代，一些被频繁使用的文件所引入的头文件会越来越多，但其中定会有一些用不上的引用。大多是删减代码或者复制代码时，直接拷贝进来的。而这些多余的引用，我目前还不知道有什么好的方法可以找出来并干掉，之前只是在code review的时候，一个一个的删除，然后对单个文件进行编译，查看报错。

### 第三条：多用字面量，少用与之等价的方法

其实作者强调的是针对NSArray，NSDictionary，NSNumber三个类的字面语法。从代码上看，字面量的确是要简洁一些。日常开发中需要注意一点，在向可变的NSArray和NSDictionary中添加数据时，一般不会使用字面量。因为，容易崩。正如作者所强调的，向NSArray，NSDictionary中添加nil会引起程序崩溃。所以，一般团队会对NSArray，NSDictionary添加分类方法，以保证加入的元素是有效的。在debgu环境下，可以直接中止程序。而且在运行时可以直接走异常逻辑，防止程序崩溃。

### 第四条：多用类型常量，少用define

作者讲了很多，其实就是在说#define、static、extern三者的差异。在功能上，三者有一定的重叠，所以，有的场景个三都都可以实现我们想要的功能，但按作者所推荐的，有的场景下是存在最优解的。

2. define：宏定义。在预处理阶段，对代码中出现的宏进行替换。宏定义存在一些问题（1）就是作者所强调的没有类型检查；（2）作者也提到的，可以被重复定义，而且不会报错；（3）直接替换，并没有减少可执行文件的大小。
2. static：静态变量。在C++中static的使用相对复杂一些。但在OC中static的使用场景已经少了很多。作者在本条中强调 static const 为只在编译单元内可见的常量。并且推荐开发者将这类常量直接放在.m文件中进行声明。反正是只给自己用的，又为啥要放在头文件中呢。如果要给外部使用，为啥不用extern呢？
3. extern：声明变量已经定义过了，放心用。基本使用也很简单，在.m文件中定义，在.h文件中导出。.h文件中导出的意义是啥？不导出可以不？当然是可以的啦。谁在使用的时候，自己声明一下也是可以的。不过，就是麻烦一些。每个用到的地方都得声明，而且定义在.m文件中，万一拿不到源代码，那如何知道里面是如何定义的呢。所以，定义放在.m文件中，声明放在.h文件中，这样每个import的用户都知道变量是啥了

当然，光这个点又可以展开一堆东西。这里点到为止。先说个在之前代码里看到过的问题吧。

1. const乱用
    ```objc
    static NSString * const firstName = @"Bear";
    static const NSString * secondName = @"Ger";
    ```
    上面二行代码的差异就是const的位置，OC中经常会见到这种定义全局的静态常量。但上面第二行的const并无意义。常量本身是不可修改的。在重复声明成const并无作用，但由于没有限制指针为常量，所以secondName可以被随意指向别的字符串。我相信，这并不是开发所想要的。

### 第五条：多用枚举

```objc
typedef NS_ENUM(NSUInteger, MyEnum) {
    MyEnumValueA,
    MyEnumValueB,
    MyEnumValueC,
};

typedef NS_OPTIONS(NSUInteger, MyEnumOption) {
    MyEnumValueAOption = 1 << 0,
    MyEnumValueBOption = 1 << 1,
    MyEnumValueCOption = 1 << 2,
};

```
呃，记得之前做过一道算法题：七只老鼠，一百瓶药水，其中有一瓶是毒药，毒发时间为一天，使用一天时间检测出毒药

### 第六条：何为“属性”

#### property + synthesize + dynamic

    1. @property声明一个属性；@synthesize对一个属性起个别名；@dynamic告诉编译器属性的存存方法我自己来，你别管。
    2. 点语法和方法调用其实是一样的。=。=按作者所说，编译器会将点语法转换成方法调用。但有个疑问：在使用XCode的po命令的时候，会遇到点语法报错，但是方法调用就可以。
    3. property声明一个属性的时候，如果没有别的干扰，编译器会生成对应的读写方法和实例变量
    4. 如果不使用dynamic，无法同时重写getter和setter方法。但加了dynamic后，存取方法和变量都得自己来维护了。之前有看到一个UserDefault的库：GVUserDefaults，它就是使用dynamic来实现的，用起来也还挺方便


#### 属性特质

[很久之前写的关于property属性](../iOSer/@property.md)

### 第七条：在对象内部尽量直接访问实例变量

虽然作者是这么建议的，但个人觉得没有100%的教条，还得出于各种不同的考量。

    1. 使用属性方法，会涉及到消息转发。所以直接方法在性能上相对要高一些，但说实在的，一般，如果不是对性能进行极限优化，这点蚊子肉，应该是不在乎的。
    2. 直接访问变量不会触发KVO。这个需要注意，因为有的时候，的确会有坑。
    3. 在初始化方法中尽量使用直接访问，作者给出的场景就是父类中的属性的存取方法被子类重写了。的确是有这么个场景，但说实在的，子类都重写了，说明人家就是要重写哇。
    4. block请直接使用属性方法，不然xcode报警告
    5. 个人在日常开发中，使用属性方法偏多。并且倾向属性使用懒加载方式。

### 第八条：对象等同性

作者提到了很多，但我在日常使用中，最多的只是字符串的等同性判断。hash更是修改得少。不过作者在最后列举的例子倒是很有趣的。不知道是作者自己YY出来的，还是说真的遇到了这个Bug。

### 第九条：以类簇隐藏实现细节

这里介绍的设计模式还是很有用的。

### 第十条：关联对象

作者的举例显然已经过时了，Alert改了又改。但我所遇到的关联对象的使用场景最多的还是在分类中添加变量。在分类中添加的属性，编译器不会为其创建属性方法和变量（有种小妾生的感觉叱），得开发者自己来。

```objc

@interface BlockDemo (Extension)
@property (nonatomic, strong) NSString * secondName;
@end

@implementation BlockDemo (Extension)

-(void)setSecondName:(NSString *)secondName {
    objc_setAssociatedObject(self, _cmd, secondName, OBJC_ASSOCIATION_COPY);
}

-(NSString *)secondName {
    return objc_getAssociatedObject(self, @selector(setSecondName:));
}
@end
```

需要注意的几点：

1. 内存管理
2. key，个人习惯直接使用sel，方便。当然_cmd也可以。其它也还还行，但存取所使用的key的指针要一样，按作者推荐的使用静态全局变量。

何时会有出现这样的场景呢？项目大了，什么鬼场景都能遇到。比如说：公司的公共框架提供了一个类，但现有类对业务支持不够，需要新添加属性。为啥不直接子类呢？因为类的创建在公共代码中，业务则没有权限修改。=。=！业务则能拿到的就是一个已经被实例化的类对象。这个时候，就可以使用分类，然后创建属性，添加关联对象。不过需要注意，通过这种方式添加属性的时候，一定要为属性添加前缀。不要现有的或者将来的属性发生冲突。当然，这并不是唯一的出路，直接给公共提需求，也是可以的。就是流程长点，时间久点。产品大大接受的话，还是保持代码的干净整洁为好。（我始终觉得关联对象，对代码有污染）

### 第十一条：objc_msgSend

这东西NB了。先偷偷看一眼长啥样。
```objc
OBJC_EXPORT id _Nullable
objc_msgSend(id _Nullable self, SEL _Nonnull op, ...)

//精简一下
id objc_msgSend(id self, SEL op, ...)
```

之有写的两个例子，第一个例子拿到类方法；第二个例子拿到实例方法；
```objc
//eg1:
Class watchSessionManager = NSClassFromString(@"TTNAppWCSessionManager");
SEL sharedInstanceSel = NSSelectorFromString(@"sharedInstance");
IMP impSharedInstance = [watchSessionManager methodForSelector:sharedInstanceSel];
id (*funcSharedInstance)(id, SEL) = (void *)impSharedInstance;
id watchInstance = funcSharedInstance(watchSessionManager, sharedInstanceSel);

SEL startSessionSel = NSSelectorFromString(@"startSession");
IMP impStartSession = [watchInstance methodForSelector:startSessionSel];
void (*funcStartSession)(id, SEL) = (void *)impStartSession;
funcStartSession(watchInstance,startSessionSel);


//eg2:
SEL selector = NSSelectorFromString(@"processRegion:ofView:");
IMP imp = [_controller methodForSelector:selector];
CGRect (*func)(id, SEL, CGRect, UIView *) = (void *)imp;
CGRect result = func(_controller, selector, someRect, someView);
```

这两个例子之前是为了解决performSelect产生的警告。放在这里方便贯穿一下。作者强调所有方法在底层其实都是C语言函数。个人猜测methodForSelector就是从方法列表中拿的函数指针。只不过这种方式，直接绕开了消息转发。

疑问：`[self sayHello]` 怎么变成`objc_msgSend(obj,sayHello)`的呢？按作者所说，这事是编译器干的。

### 第十二条：消息转发

三板斧：动态解析、求援、完整消息转发。要是三个都搞不定，那就GG了。消息转发在实际使用中并不常见。之前在看iOS一个XMPP库源代码的时候，看到过。但至今还没有遇到必须使用的场景。看来还是经历太少了。这里也就玩玩。

前面讲到的向分类中添加属性或者使用dynamic修饰。编译器不会添加存取方法，而且如果我们自己也不添加。那就会走到这个消息转发流程中。


**动态解析**

还是上面的例子，我们改一改：
```objc
@implementation BlockDemo (Extension)
+(BOOL)resolveInstanceMethod:(SEL)sel {

    if ([NSStringFromSelector(sel) isEqualToString:@"setSecondName:"]) {
        
        class_addMethod(self, sel, [self instanceMethodForSelector:@selector(setSecondNameDynamic:)], "v@:@");
        return YES;
        
    }
    else if([NSStringFromSelector(sel) isEqualToString:@"secondName"]) {
        class_addMethod(self, sel, [self instanceMethodForSelector:@selector(secondNameDynamic)], "@@:");
        return YES;
    }
    else {
        return [super resolveInstanceMethod:sel];
    }
}

-(void)setSecondNameDynamic:(NSString *)secondName {
    objc_setAssociatedObject(self, _cmd, secondName, OBJC_ASSOCIATION_COPY);
}

-(NSString *)secondNameDynamic {
    return objc_getAssociatedObject(self, @selector(setSecondName:));
}
@end
```

**求援**

这个的意思大概就是说我处理不了，换个人吧。作者在书中提到可以使用这个方法实现多继承。想了想。感觉可以玩。

.h
```objc
@protocol PM <NSObject>
-(void)writeDemand;
@end

@protocol RD <NSObject>
-(void)doDemand;
@end

@protocol UED <NSObject>
-(void)designUI;
@end

@protocol QA <NSObject>
-(void)testProduct;
@end

@interface SuperMan : NSObject  <PM,RD,UED,QA>

@end
```

.m
```objc
#import "SuperMan.h"

@interface IamPM : NSObject

@end

@implementation IamPM
-(void)writeDemand {
    NSLog(@"%@",NSStringFromSelector(_cmd));
}
@end

@interface IamRD : NSObject

@end

@implementation IamRD
-(void)doDemand {
    NSLog(@"%@",NSStringFromSelector(_cmd));
}
@end

@interface IamUED : NSObject

@end

@implementation IamUED
-(void)designUI {
    NSLog(@"%@",NSStringFromSelector(_cmd));
}
@end

@interface IamQA : NSObject

@end

@implementation IamQA
-(void)testProduct {
    NSLog(@"%@",NSStringFromSelector(_cmd));
}
@end



@implementation SuperMan

-(id)forwardingTargetForSelector:(SEL)aSelector {
    if ([NSStringFromSelector(aSelector) isEqualToString:NSStringFromSelector(@selector(writeDemand))]) {
        return [IamPM new];
    }
    else if ([NSStringFromSelector(aSelector) isEqualToString:NSStringFromSelector(@selector(doDemand))]) {
        return [IamRD new];
    }
    else if ([NSStringFromSelector(aSelector) isEqualToString:NSStringFromSelector(@selector(designUI))]) {
        return [IamUED new];
    }
    else if ([NSStringFromSelector(aSelector) isEqualToString:NSStringFromSelector(@selector(testProduct))]) {
        return [IamQA new];
    }
    return nil;
}

@end
```

调用
```objc

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        SuperMan * superMan = [SuperMan new];
        [superMan writeDemand];
        [superMan doDemand];
        [superMan designUI];
        [superMan testProduct];
    }
    return 0;
}
```

当然，只是YY一下。平时基本不用这个，个人觉得程序走到这里，已经算是异常流了。功能拆分，可以直接使用分类就发了。

**完整消息转发**

在这里我们能拿到的不只是SEL了，而是一个经过完整包装的NSInvocation对象。我们可以对这个对象进行操作。
```objc
-(NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector {
    if ([NSStringFromSelector(aSelector) isEqualToString:@"setSecondName:"]) {
        
        
        return [NSMethodSignature signatureWithObjCTypes:"v@:@"];
        
    }
    else if([NSStringFromSelector(aSelector) isEqualToString:@"secondName"]) {
        
        return [NSMethodSignature signatureWithObjCTypes:"@@:"];
    }
    else {
        return nil;
    }
}

-(void)forwardInvocation:(NSInvocation *)anInvocation {
    
}

```

日志
```objc
Printing description of anInvocation:
<NSInvocation: 0x100607d20>
return value: {v} void
target: {@} 0x102866110
selector: {:} setSecondName:
argument 2: {@} 0x100002108
(lldb) po self
<BlockDemo: 0x102866110>

(lldb)
```

至于就NSInvocation中的内容如何变更，很久之前看过，但平时基本不用。这里略过了。这里需要强调的是，如果自己类的消息转发处理不了，记得调用super，别死撑。

### 第十三条：method swizzling

这个概念之前被用得很多<黑魔法>，还有个库叫Aspect，有兴趣可以阅读了解一下。是的，只是阅读了解，并不是学习。因为在大的项目中，老老实实写代码才是正道。把项目搞复杂了，只是给后来人挖，还可能坑到自己。不过作者提到的黑盒调试技巧在一些场景下还是很有用的。之前在逛Github的时候，发现有个截图仓库，它就什么也没有，就是直接拿了腾讯的截图framework，然后hack了库的方法。

这东西虽然功能强大，但一般用上不，像原子弹。

### 第十四条：类对象

 [Objective-C Runtime 运行时之一：类与对象](http://www.cocoachina.com/ios/20141031/10105.html)

**元类**

meta-class是一个类对象的类。简单地说，当你发送一条消息给一个对象时，这条消息会在对象的类的方法列表里查找；当你发送一条消息给一个类时，就会在类的meta-class的方法列表理查找消息；meta-class是必不可少的，因为它存储了一个类的类方法。每个类都必须只有唯一的meta-class,因为每个类都只可能有一个唯一的类方法列表。

### 第十五条：命名添加前缀

多加注意就好。一种命名规范。

### 第十六条：全能初始化方法

### 第十七条：实现description方法

这个就是方便查看内容，挺有用的。在这里可以甄选几个关键的信息打印出来，也可以使用运行时，将实例的属性格式化的打印出来。下面是从网上抄的。差不多就是这个思路了。可以根据自己的喜好，修改一下打印输出的样式。
```objc
- (NSString *)debugDescription{
    
    NSMutableDictionary *dict = [[NSMutableDictionary alloc]init];
    
    uint count;
    objc_property_t *properties = class_copyPropertyList([self class], &count);
    
    for (int i = 0; i < count; i++) {
        
        NSString *name = [NSString stringWithFormat:@"%s",property_getName(properties[i])];
        
        id value = [self valueForKey:name] ?:@"nil";
        
        [dict setObject:value forKey:name];
    }
    
    free(properties);
    return [NSString stringWithFormat:@"<%@:%p>:%@",[self class],self,dict];
}
```

### 第十八条：尽量使用不可变对象

实现的时候就是在.h中声明为readonly，然后在extension中再声明为readWrite。但是使用场景各有不同，个人觉得不强制。

### 第十九条：命名规范

之前有看过官方的命名规范，[Introduction to Coding Guidelines for Cocoa](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html#//apple_ref/doc/uid/10000146-SW1)

内容还是很多的，但总的来说，就是清楚明白，让代码像口语一样无歧义，通顺。

### 第二十条：为私有方法添加前缀

呃，没这么做过。而且OC的动态性，也无法强制私有。平时最多添加`#pragma mark - private`，然后将私有的放在一起。

### 第二十一条：错误模型

1. 对于致命的问题，直接崩就好了
2. 对于其它问题，返回nil/0,NSError对象。

### 第二十二条：NSCoping

平时用得很少，主要关注是深拷贝还是浅拷贝；返回的是可变对象，还是不可变对象。

### 第二十三条：protocol、delegate

有过iOS开发经验的，相信对这身份个的用法并不陌生。不过这种方式在block出来之后，就大量的被block取代了。很久之前在看AFNetworking的代码的时候，从整个git提交中可以发现其中有一次大改版就是由于block的出现（虽然那个时候我还没有接触编程）。但整个代码结构在使用block之后变得简洁了很多，有兴趣的可以去查看一下。

但其实delegate还是很多地方会用到的，我个人很多时候也会优先使用delegate。主要是原因是block没有使用xcode进行代码跳转。项目中很多block，可是执行到的block的时候，还不知道哪里来的，找也得找一会。=。=而如果使用delegate就清清楚楚了。

系统提供的接口也有很多是使用delegate的，像tableView这些，用起来也还是很顺手的。所以在delegate和protocol之间选哪个，看场景和个人喜好了。作者在本条中所举的例子，基本已经确认被block替换掉了。

这里需要强调的是delete需要weak。

### 第二十四条：分类

1. 使用分类拆分功能
2. 可以创建一个名为private的分类。但其实这样做有点掩耳盗铃。因为，oc做不到私有一说。需要调用的时候，照样该怎么玩怎么玩。总不能跟产品大大说，公共框架不支持，没法实现吧。

关于分类，之前在项目中看到这样一种用法，个人不推荐使用，但吃瓜群众一起看看吧。
```objc
@implementation BlockDemo
-(void)privateMethod {
    NSLog(@"hello");
}
@end
```
场景大概是这样的，有上面的这个类，但他内部有一个方法，我想调用。当然，直接使用`performSelector`也是可以解决一些问题的。但`performSelector`对于传递参数是有痛点的。这个时候分类就可以玩出花来。

```objc
@interface BlockDemo (XXX)
-(void)privateMethod;
@end
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        BlockDemo * demo = [BlockDemo new];
        if ([demo respondsToSelector:@selector(privateMethod)]) {
            [demo privateMethod];
        }
    }
    return 0;
}
```

可以看到，直接对原来的类创建一个分类，然后场明一个相同的方法，但不写实现。这样一来，就可以直接进行方法调用了。**注意这里的保护，防止哪开公共把这个方法删了，就尴尬了**。一般情况下，不推荐这么玩，但总有不一般的时候。项目大了，什么鬼场景都有的。

使用分类拆分功能，会带来另外一个问题。分类无法访问extension中的属性。导致，功能拆分后，属性也需要放在头文件中。之前在拆分项目代码的时候就遇到这个问题。还挺烦人的。因为很多属性并不想暴露出来。


### 第二十五条：分类的前缀

因为分类的方法容易撞车。分类的方法和主类的方法最终都进了主类的方法列表中。按栈进行存储。所以先加载主类的方法会被分类的方法给覆盖掉。而且分类与分类之前也容易出现方法覆盖。所以，加上前缀吧。

这里聊一下之前遇到的坑。类ClassA有一个方法MethodA，然而，这个方法是空的。显然，这是说子类你自己玩吧，我不管了。于是我在子类中写好了。但是，公共代码中ClassA的一个分类直接将这个方法复写了。(T_T WTF,每次遇到这样的问题都得搞几个小时)

### 第二十六条：不要在分类中声明属性

前面有提到在分类中添加属性的方法。但这里就尴尬了，作者是不推荐这么玩的。而原因就是使用关联对象玩这个容易出坑，就像是让一个没有学习过C++的iOS开发去写C++项目样。虽然容易上手，但内存管理上内容出事。虽然作者不推荐，但，该用咱还得用。

### 第二十七条：extension隐藏细节

没啥好说的。大家都这么用的。不过在第24条中我有提到使用分类，可以破解这种封装。=。=！哎，不能老老实实的写代码了。

但该开放细节，不要隐藏起来，要不使用的人内心是奔腾的。比如之前遇到这样一个场景：父类实现了一个protocol，但将这个写在extension中。子类对此一无所知，于是子类中又写了一次。但从功能上来说，这个protocol就应该让外界知道。

在权衡是开放还是隐藏的时候，需要根据场景来说。不要一味的隐藏，也不要一味的开放。

### 第二十八条：通过协议提供匿名匿名对象

这条可以理解为面向协议编程。使用者在拿到delegate的时候，并不关心他是什么对象。只关心说这个delegate实现了某个协议，而基于此我可以这么用它。至于说它是谁，并不重要。

### 第二十九条：引用计数 + 第三十条：ARC

呃，这个就大头了。之前在学习C++的时候，C++为了解决同样的问题，有一个方案叫智能指针。两个有点类似，有兴趣可以去了解一下。

由于OC现在已经是ARC的天下了，所以平时只要知道有这个东西，了解其原理，可以通过XCode查看引用计数分析内存泄漏的问题就好了。

当内存有泄漏的时候，我一般习惯用xcode自带的Instrument Tool memory leak进行查找，在这里可以直接看到引用计数。查找起来虽然有点低效（得自己一点一点的分析），但基本都可以定位到问题所在。

###  第三十一条：dealloc

1. 不用调用super dealloc
2. 不要使用存取方法，使用话，可能遇到奇怪的问题
3. dealloc不一定被调用，还得得NSTimer这个大坑不？
4. 清理掉需要手动清理的资源：kvo，iOS9之前的Notfiy，等等。
5. 当引用计数变为0的时候，会被系统调用。所以在MVC模式中，VC应该是最先被调用的。平时开发页面时，最后检查一下VC和重要的View的dealloc是不是被调用了是一个很好的习惯。

### 第三十二条：异常处理时的内存问题

呃，没用过，也很少见到，略略略~
```objc
@try {
    
} @catch (NSException *exception) {
    
} @finally {
    
}
```

### 第三十三条：weak

这里强调的是weak和unsafe_unretain的差异。现在在代码里已经很少看到unsafe_unretain了。使用weak可以解析循环引用的问题。但作者强调的一点需要注意：如果weak对象已经被释放了，那应该是代码有问题。

上面提到的代理模式中，weak就是很必要的。

### 第三十四条：@autoreleasepool

很少遇到。但如果使用了libextobjc这个库的话，其实到处都在用。之前在分析@weakify的实现时写过[@weakify](../iOSer/@weakify.md)。那么问题来了，如果@autoreleasepool是有成本的，那满开飞的@weakify和@strongify是不是也有性能损耗。

这条中作者介绍了autoreleasepool可以解决在for循环中内存峰值的问题。

### 第三十五条：zoombie + 第三十六条：retainCount 

略

### 第三十七条：block + 第四二十条：block的循环引用问题

之前写的和摘抄的关于block的文章。这里就不再简述了。

* [Block](../iOSer/block.md)
* [Block-What’s Block](../iOSer/什么是Block.md)
* [Block-变量捕获](../iOSer/变量捕获.md)
* [Block-Type](../iOSer/BlockType.md)
* [Block-@weakify](../iOSer/@weakify.md)

### 第三十八条：typedef block

简单的，不会被反复使用的时候，我会直接写block，但，如果会被多处用到，使用typedef还是要好很多的。block的命名不要太简单。我一般选择关联的类做为前缀：`SuperManDoneWorkResultBlock`。虽然有些长，但不会撞车。安全第一。

### 第三十九条：使用block使功能聚合

之前在使用delegate的话，方法的调用和对应的回调分散在两个地方。阅读时得跳来跳去了。但如果使用block的话，就没有这个问题了。最常用的大概就是接口请求了。

### 第四十一条：多用GCD，少用同步锁

`@synchronized`存在效率问题，所以作者不推荐使用。平台一般也不会这个，因为一般来说用不上锁，真正用到的时候`@ynchronized`这货靠不住。

之前摘录过一篇关于[Lock](../iOSer/Lock.md)的文章，可以参照一下。

### 第四十二条：多用GCD，少用performSelector

使用performSelector时会出现 `performSelector may cause a leak because its selector is unknown` 警告。原因作者在这一条里写得比较清楚了。但在有的场景下，performSelector还是很好用的。比如，我在写蓝牙库的时候，底层有50多个蓝牙指令，根据不同的流程会执行不同的指令。所以，流程和指令是绑定的。为了简化维护，我是直接创建了一个流程和selector的字典。然后在一个地方进行统一调用。

关于performSelector的警告，上面有提到使用IMP调用C方法的形式进行处理。但还是建议少用这个。一是不好用（局限性），二是容易挖坑。

### 第四十三条：GDC and Operation queue

经历有限，这部分的内容很少用到。平时也大我时使用GDC。不过作者在这里列举了Operation queue的优势
1. 取消某个操作
2. 指定操作间的依赖关系
3. Operation queue的有引起属性支持KVO
4. 指定操作的优先级
5. 自定义NSOperation子类，达到代码复用

了解了以上几点优势，后面在支持选型的时候，就有所参考了。但说实在的，平时如果没有什么要求，基本都用的GDC。

之前收录的一篇关于[NSOperation](../iOSer/NSOperation.md)的文章，主要介绍了NSOperation的基本使用。

之前收录的关于[GCD多线程](./iOSer/GCD多线程.md)的文章

### 第四十四条：dispatch group

看个例子吧：
```objc
    dispatch_semaphore_t semaphore = dispatch_semaphore_create(0);
    dispatch_group_t group = dispatch_group_create();
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    NSMutableArray * fileNames = [NSMutableArray new];
    
    [fileNames addObject:[BlockDemo new]];
    [fileNames addObject:[BlockDemo new]];
    [fileNames addObject:[BlockDemo new]];
    [fileNames addObject:[BlockDemo new]];
    
    [fileNames enumerateObjectsUsingBlock:^(BlockDemo *  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        
        dispatch_group_async(group, queue, ^{
            [obj start:^{
                dispatch_semaphore_signal(semaphore);
                NSLog(@"done%ld",idx);
            }];
        });
        
    }];
    
    dispatch_group_notify(group, queue, ^{
        for (int i = 0; i<fileNames.count; i++) {
            dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
        }
        NSLog(@"done");
    });
```
这个例子中加了信号量，如果start是一个同步执行的任务，信号量是可以用的。但如果是异常的，这个就是必要的。比如在start中进行了网络请求。而这个例子的作用就是并发执行多个网络请求，等所有请求结束后才进行后面的行为。虽然对于这个场景，我一般会要求服务端组合接口，而不是端上这么玩。但总有遇到类似的场景。信号量和dispatch group结合，还是很有用的。

### 第四十五条：dispatch_once

目前遇到的坑就是这里偶现崩溃。还是崩在系统层面。但是不确定是哪里的原因。

### 第四十六条：不要使用dispatch_get_current_queue

没用过，没见过，没感觉~！略

### 第四十七条：系统框架

没事多Google、多百度、多看文章。对系统框架有个大概的影响。等后面用到的时候，知道系统已经实现了这个。

### 第四十八条：多用块枚举，少用for循环

声明collection的时候，最好把类型也加上。`NSMutableArray<BlockDemo*> * fileNames = [NSMutableArray new];`这个在添加的时候就有类似校验，遍历的时候也方便。

### 第四十九条：Foundation <=> __bridge  <=> CoreFoundation

平时用得不多，在网上找的一篇文章：[__bridge](../iOSer/__bridge.md)。但我记得swfit中已经将这部分内容全封装起来我，swift开发不用关心这些了。

### 第五十条：缓存使用NSCache

平时用的不多，只在特定场景才需求。相关实例可以参考：SDWebImage和AFNetWorking。

### 第五十一条：initialize & load 中的代码要精简

之前写的关于[initialize与load](../iOSer/initialize与load.md)的文章

### 第五十二条：NSTimer会对目标对象强引用

所以，不要想着在目标对象的dealloc中停止timer。因为dealloc不会被执行。











