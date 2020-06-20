原文：http://www.qingpingshan.com/rjbc/ios/157477.html

# 引言

Core Foundation框架 (CoreFoundation.framework) 是一组C语言接口，它们为iOS应用程序提供基本数据管理和服务功能。下面列举该框架支持进行管理的数据以及可提供的服务：

1. 群体数据类型 (数组、集合等)
2. 程序包
3. 字符串管理
4. 日期和时间管理
5. 原始数据块管理
6. 偏好管理
7. URL及数据流操作
8. 线程和RunLoop
9. 端口和soket通讯

Core Foundation框架和Foundation框架紧密相关，它们为相同功能提供接口，但Foundation框架提供Objective-C接口。如果您将Foundation对象和Core Foundation类型掺杂使用，则可利用两个框架之间的 “toll-free bridging”。所谓的Toll-free bridging是说您可以在某个框架的方法或函数同时使用Core Foundatio和Foundation 框架中的某些类型。很多数据类型支持这一特性，其中包括群体和字符串数据类型。每个框架的类和类型描述都会对某个对象是否为 toll-free bridged，应和什么对象桥接进行说明。

Toll-free bridging 是ARC下OC对象和Core Foundation对象之间的桥梁

在开发iOS应用程序时我们有时会用到Core Foundation对象，下面简称CF。例如Core Graphics、Core Text，并且我们可能需要将CF对象和OC对象进行相互转化，我们知道，ARC环境下，编译器不会自动管理CF对象的内存，我们需要手动管理。这就是我们在创建一个CF对象以后需要我们使用CFRelease将其手动释放。

那么CF和OC相互转化的时候该如何管理内存呢？

我们可以通过 bridge, bridge_transfer,__bridge_retained 来进行内存管理

# __bridge

CF和OC对象转化时只涉及对象类型不涉及对象所有权的转化
```objc
//Image I/O 从 NSBundle 读取图片数据
NSString *imagePath = [[NSBundle mainBundle] pathForResource:@"image" ofType:@"png"];

NSURL * url = [NSURL fileURLWithPath:[[NSBundle mainBundle] pathForResource:@"image" ofType:@"png"]]

CGImageSourceRef source = CGImageSourceCreateWithURL((__bridge CFURLRef)url, NULL);
```
如果上面不添加 bridge ，在ARC环境下，系统会给出错误提示和错误修正，点击错误提示的话，系统会为我们自动添加 bridge ，因为在OC与CF的转化时只涉及到对象类型没有涉及到对象所有权的转化，所以上述代码不需要对CF的对象进行释放，即不需要添加CFRelease

> 注释： iOS ARC 和 非ARC 之间的转换方法  
> 1. 选择项目中的Targets，选中你所要操作的Target，  
> 2. 选Build Phases，在其中Complie Sources中选择需要ARC的文件双击，  
     并在输入框中输入：-fobjc-arc，如果不要ARC则输入：-fno-objc-arc

为了解决这一问题，我们使用 __bridge 关键字来实现id类型与void*类型的相互转换。
```objc
id obj = [[NSObject alloc] init];
void *p = (__bridge void *)(obj);
NSLog(@"obj retainCount %ld",[(id)p retainCount]);
// 输出结果：
CFDemo[2932:777997] obj retainCount 1
```

# __bridge_transfer

常用在CF对象转化成OC对象时，将CF对象的所有权交给OC对象，此时ARC就能自动管理该内存,作用同CFBridgingRelease()

如果非ARC的时候，我们可能需要写下面的代码。
```objc
// p 变量原先持有对象的所有权
id obj = (id)p;
[obj retain];
[(id)p release];

那么ARC有效后，我们可以用下面的代码来替换：
// p 变量原先持有对象的所有权
id obj = (__bridge_transfer id)p;
```
可以看出来， bridge_retained 是编译器替我们做了 retain 操作，而 bridge_transfer 是替我们做了 release。


# __bridge_retained

__bridge_transfer 相反，常用在将OC对象转化成CF对象，且OC对象的所有权也交给CF对象来管理，即OC对象转化成CF对象时，涉及到对象类型和对象所有权的转化，作用同CFBridgingRetain()
先来看使用 __bridge_retained 关键字的例子程序：
```objc
id obj = [[NSObject alloc] init];
void *p = (__bridge_retained void *)obj;
```
此时retainCount 会被加1；

从名字上我们应该能理解其意义：类型被转换时，其对象的所有权也将被变换后变量所持有。如果不是ARC代码，类似下面的实现：
```objc
id obj = [[NSObject alloc] init];
void *p = obj;
[(id)p retain];
// ARC如何获取retainCount
NSLog(@"Retain count is %ld", CFGetRetainCount((__bridge CFTypeRef)myObject));
```

# 栗子
```objc
NSString *string = [NSString stringWithFormat:@""];
CFStringRef cfString = (__bridge CFStringRef)string;
CFStringRef cfStr = (__bridge_retained CFStringRef)string;
CFRelease(cfString);// 由于Core Foundation的对象不属于ARC的管理范畴，所以需要自己release
CFRelease(cfStr);
```

使用 __bridge_retained 可以通过转换目标处（cfStr）的 retain 处理，来使所有权转移。即使 string 变量被释放，cfString变量也变释放，cfStr 还是可以使用具体的对象。只是有一点，由于Core Foundation的对象不属于ARC的管理范畴，所以需要自己release。

```objc
CFStringRef cfString= CFURLCreateStringByAddingPercentEscapes( NULL, (__bridge CFStringRef)text, NULL, CFSTR("!*’();:@&=+$,/?%#[]"), CFStringConvertNSStringEncodingToEncoding(NSUTF8StringEncoding));
NSString *ocString = (__bridge_transfer CFStringRef)cfString;
```
所有权被转移的同时，被转换变量将失去对象的所有权。当Core Foundation对象类型向Objective-C对象类型转换的时候，会经常用到 __bridge_transfer 关键字。

总结：

1. Core Foundation 对象类型不在 ARC 管理范畴内
2. Cocoa Framework::Foundation 对象类型（即一般使用到的Objectie-C对象类型）在 ARC 的管理范畴内 
3. bridge， bridge_transfer和__bridge_retained 是CF和OC的桥梁。
4. 如果不在 ARC 管理范畴内的对象，那么要清楚 release 的责任应该是谁以及各种对象的生命周期是怎么样的
