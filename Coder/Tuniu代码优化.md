# 代码整理

## 臃肿
.m文件代码量过大，最多的达到2000多行代码

### 原因：
* 继承机制使得大量公共方法被添加到base中。
* UI组件的代理方法没有被独立抽取出来。这个的问题最大。
由于是聊天页面，所以会存在许多种cell类型（十几种吧），之前cell的所有代理全是VC本身，没有被抽离出来。导致BaseViewController中存在大量的代理方法。这其中有的代码方法Base中有实现，有的没有。没有实现的代理方法在子类中进行了实现。导致代理方法无法进行统一抽离。
* 一些功能型方法直接写在BaseViewController中，eg:图片裁剪

### 解决方案：
* 整理Base中的公有方法和私有方法，将私有方法和公有方法分别抽离到一个独立的category中。形成privateCategory 和 publicCategory两个分类。privateCategory只被Base所依赖。publicCategory提供给Base及子类调用。
* 将Base中输入框相关的方法和代理抽离，形成InputViewHandler，对输入框进行统一管理
* 将功能型方法移出，图片裁剪这样的方法放到UIImage的分类中。
* 由于Cell的代理和BaseVC的继承体系绑定得太紧，并且cell类型过多，所以，没有将Cell的代理方法抽离，而是使所有Cell都继承自统一的BaseCell，在初始Cell时，直接将Cell的代理绑定到BaseVC上。

### 状态属性太多
客服聊天页面中有太多的状态（20种吧），将这些状态统一抽离到独立的stateManager中进行管理。

### 警告
最开始，整个消息模块有300多个Xcode警告。
警告内容包括循环引用、不匹配的变量类型、mutable和immutable进行错误赋值、未初始化的成员变量、performselector调用方法引用的警告、未使用的变量

### 多余代码
* 部分子类中直接复制了父类的方法实现 
* 群聊业务分“小跟班”和“兴趣组”，这两个聊天页面基本一样，代码95%相同，可是被分成了两个不同的类。
* 一些功能性的代码被多个类重复实现
* 空的方法或者可以看做是空的方法：`viewdidload`中没有加入自己的逻辑/`numberOfSectionsInTableView`这种直接`return 1`
* 从主仓库复制过来的分类方法。


# 性能优化

性能优化从三个方面：时间、空间、CPU、循环引用检查

通过Leaks,Allocations检查内存泄漏

通过Xcode Instruments TimeProfiler对代码进行性能检测。查看CPU占比，运行时间。

Leaks（泄漏）：一般的查看内存使用情况，检查泄漏的内存，并提供了所有活动的分配和泄漏模块的类对象分配统计信息以及内存地址历史记录；
Time Profiler（时间探查）：执行对系统的CPU上运行的进程低负载时间为基础采样。

Allocations（内存分配）：跟踪过程的匿名虚拟内存和堆的对象提供类名和可选保留/释放历史；

Activity Monitor（活动监视器）：显示器处理的CPU、内存和网络使用情况统计；

根据检测到的问题作出如下优化：

1. 懒加载
初次进行页面时，将不用马上显示的视图组件进行懒加载
2. 缓存高度
图片的高度第一次处理结束后，与对象一起保存到数据库中
3. sizeToFit
聊天页面的内容是高度复用的。label使用SizeToFit进行处理，在cell复用时，这个方法会相当占用资源。如图片一样，计算完宽度后，保存到数据库中。
4. 气泡背景
之前是`[UIImage imageNamed:xxx]`这种形式来设置气泡背景的，可是这样每个cell在被复用的时候都需要创建UIImage对象。虽然iOST系统对通过`imageNamed`方法调用的图片会缓存到内存，但我之前所做实验[[imageNamed  && imageWithContentsOfFile:]]表明，新的UIImage对象是对系统内存管理的对象的拷贝。无论是时间还是空间上都应该将气泡背景抽取到成员变量这一层
5. 聊天表情
这个和气泡背景一样的问题。所以我将表情放到缓存中进行管理
6. placeholder
这个和上面两个的问题是一样的，只不过这个更隐藏。
7. 使用MLeakFinder
进行循环引用查找
8. 对于一些经常被调用的函数,换成Imp直接调用 [[performSelector的C实现方式]]



