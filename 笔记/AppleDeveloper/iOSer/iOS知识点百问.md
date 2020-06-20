# iOS知识点百问

1. UIWindow和UIView和 CALayer 的联系和区别?
    
   * UIWindow -> UIView -> UIResponder -> NSObject
   * CALayer -> NSObject
   * UIView.layer isa CALayer

2. property 都有哪些常见的字段, 各自的特点与区别

	1. 线程安全：atomic / nonatomic ::默认为atomic::
	2. 读写控制：readonly / readwrite ::默认为readwrite::
	3. 内存管理：retain / assign / strong / weak / unsafe_unretained / copy
	4. 读写方法：setter / getter 用于set和get方法重命名

3. block和weak修饰符的区别

   1. `__block`不管是ARC还是MRC模式下都可以使用，可以修饰对象，也可以修饰基本数据类型
   2. `__weak`只能在ARC模式下使用，只能修饰对象（NSString），不能修饰基本数据类型
   3. __block修饰的对象可以在block中被重新赋值，weak修饰的对象不可以
   4. weak修饰的对象被释放后，weak指针会被置为nil

4. 常见的 Http 状态码有哪些？

    1xx临时响应；2xx请求成功；3xx重定向；4xx请求错误，找不到服务器；5xx服务器错误；

5. 单例的写法。在单例中使用数组要注意什么？

    数组需要使用strong来修饰。不然单例中的数组的内容会被释放掉，这和单例的意图不一样了

6.  static 关键字的作用

    static变量存在全局静态区。所以变量的生命周期发生了变化。static还会限制变量的作用域。static的初始化语句只会执行一次。

7.  iOS 中的事件的传递:响应链

    以点击事件为例：点击 -> UIKit生成事件对象 -> 将事件添加到主线程任务队列 -> UIApplicaion将事件发送给响应组件
    
    查找过程主要有两个函数：`hitTest:withEvent:` 和`pointInside:withEvent:`

8.  堆和栈的区别

    从管理方式来讲
  	1. 对于栈来讲，是由编译器自动管理，无需我们手工控制；
  	1. 对于堆来说，释放工作由程序员控制，容易产生内存泄露(memory leak)
    1. 从申请大小大小方面讲
    1. 栈空间比较小
    1. 堆控件比较大
    1. 从数据存储方面来讲
    1. 栈空间中一般存储基本类型，对象的地址
    1. 堆空间一般存放对象本身，block的copy等

9. Object-c的类可以多重继承么?可以实现多个接口么?Category是什么?重写一个类的方式用继承好还是分类好?为什么?

    类别与类的同名方法会覆盖类的方法，这样就不方便调用类的方法。分类是方法的覆盖，再想调用原来的方法就难了（可以通过运行时，去类的方法列表中查找，但一般不这么玩了）

10. ` #import` 跟` #include` 又什么区别，@class呢,` #import<>` 跟 `#import ""`又什么区别?

	1. import会包含这个类的所有信息，包括实体变量和方法，而@class只是告诉编译器，其后面声明的名称是类的名称，至于这些类是如何定义的，暂时不用考虑，后面会再告诉你。
	2. 在头文件中， 一般只需要知道被引用的类的名称就可以了。 不需要知道其内部的实体变量和方法，所以在头文件中一般使用@class来声明这个名称是类的名称。 而在实现类里面，因为会用到这个引用类的内部的实体变量和方法，所以需要使用#import来包含这个被引用类的头文件。
	3. 在编译效率方面考虑，如果你有100个头文件都#import了同一个头文件，或者这些文件是依次引用的，如A–>B, B–>C, C–>;D这样的引用关系。当最开始的那个头文件有变化的话，后面所有引用它的类都需要重新编译，如果你的类有很多的话，这将耗费大量的时间。而是用@class则不会。
	4. 如果有循环依赖关系，如:A–>B, B–>;A这样的相互依赖关系，如果使用#import来相互包含，那么就会出现编译错误，如果使用@class在两个类的头文件中相互声明，则不会有编译错误出现。

11. id 声明的对象有什么特性?

    可以在运行时转换成任意其它OC类型。

13. `Objective-C`如何对内存管理的,说说你的看法和解决方法?

    Objective-C的内存管理主要有三种方式ARC(自动内存计数)、手动内存计数、内存池。MRC 手动引用计数;ARC 自动引用计数,现在通常ARC;通过 retainCount 的机制来决定对象是否需要释放。 每次 runloop 最后，都会检查对象的 retainCount，如果retainCount 为0，说明该对象没有地方需要继续使用了，可以释放掉了。

14. MVC设计模式是什么？ 你还熟悉什么设计模式？

	1. 单例(NSNotificationCenter, NSUserdefault, UIScreen)
	6.  代理(delegate)
	7.  装饰器（Category）
	8.  适配器（NSCoding）
	9.  外观（JDBC,用于封装多种不同的实现，提供统一的接口）
	10. 工厂（类族）
	11. 责任链(响应链)
	12. 命令（NSOperation）
	13. MVC/MVVM
	14. 观察
	15. 代理

15. 浅复制和深复制的区别?

16. 类别的作用?继承和类别在实现中有何区别?

17. 类别和类扩展的区别。

	1. extension可以有变量+属性+方法。
	2. category只能有方法+属性声明

18. OC中的协议和java中的接口概念有何不同?

	协议必须实现`@requied`和可选实现`@optional`

18. 什么是KVO和KVC?

	KVC更多的是一种编程格式和编程接口。操作系统会根据key按照不同的命名方式进行查找。提供了一整套API进行操作

19. 代理的作用?
    
	代理一定会提供接口来完成一些功能。所以代理就是为用户（调用方）提供一些功能。除了代理所提供的接口，用户不需要知道更多的信息。算是面向接口编程的一种实现吧。

20. OC中可修改和不可以修改类型。

	mutable and imutable

21. 我们说的oc是动态运行时语言是什么意思?

	1. 多态：在运行时确定对象的类型，将父类指针指向子类对象
	2. 运行时系统：运行时消息转发，以及动态创建类对象

22. 通知、协议、KVO的不同之处?

	都是用来接收反馈。

23. 关于多态性
    
	父类指针指向子类对象

24. 对于单例的理解

25. 响应链

26. frame和bounds有什么不同?

27. 方法和选择器有何不同?

28. OC的垃圾回收机制?
    
	iOS上没有，Mac OS上有。但后来10.8Mac上也不用了。统一使用引用计数

29. NSOperation queue?

30. 什么是延迟加载?

	用到的时候再加载，需要注意不在要创建的方法体内使用`self.xxx`。根据之前的经验这样写好像是没有问题。但从表面上看这会导致循环访问。

31. 一个tableView是否可以关联两个不同的数据源?你会怎么处理?
    
	tableview的数据源是以代理的方式提供的。在`cellForItemAtIndex:`中根据条件提供不同的数据源就好了。当然还有`numberOfRowsInSection:`

32. 什么时候使用NSMutableArray，什么时候使用NSArray?

	考察可变不可变，深拷贝和浅拷贝的

33. 给出委托方法的实例，并且说出UITableVIew的Data Source方法

34. 在应用中可以创建多少autorelease对象，是否有限制?
    
	app总有一个autoreleasepool，但一个pool里有限制？？？应该没有。对象的创建是放在堆里的。除非堆爆了，应用被系统杀掉了
	```obj-c
    #import <UIKit/UIKit.h>
    #import "AppDelegate.h"
    
    int main(int argc, char * argv[]) {
        @autoreleasepool {
            return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
        }
    }
	```

35. 如果我们不创建内存池，是否有内存池提供给我们?

36. 什么时候需要在程序中创建内存池?

	内存池主要解决在一个程序段内，内存急剧增涨。

37. 类NSObject的那些方法经常被使用?
	
	1. `+ load`
	2. `+ initialize`
	3. `- copy`
	4. `- mutableCopy`
	5. `+ new`
	6. `+ isSubclassOfClass:`
	7. `- performSelectorOnMainThread:withObject:waitUntilDone:`
	8. `- isKindOfClass:`
    
38. 什么是简便构造方法?

39. UIView的动画效果有那些?

	呃，这种问题考察api背得好不好吧~！平时直接查Api不就好了。个人觉得UIView的动画效果和PPT提供的那一套差不多。

40. UIView哪些属性支持动画效果

	frame、bounds、center、transform、alpha、backgroundColor

41. 在iPhone应用中如何保存数据?
    
	1. NSKeyedArchiver
	2. NSUserdefault
	3. 数据库
	4. 服务器
	5. 文本文件

42. 什么是CoreData? NSManagedObject? NSManagedObjectContext?

43. 什么是谓词? NSPredicate
    
	提供了一套接口可以方便的对数组进行过滤，判断条件

44. 谈谈对Block 的理解? block 实现原理

45. 做过的项目是否涉及网络访问功能，使用什么对象完成网络功能?

46. 多线程是什么? iOS 中的多线程?
    
	1. NSThread
	2. NSOperation
	3. GCD

47. 在项目什么时候选择使用GCD，什么时候选择NSOperation?
    
	两个的作用粒度不一样。GCD更多的是注重队列级别的，任务一但添加到队列中，就很难再操作了。而NSOperation则是一个任务级别的。可以对任务进行更加细致的操作。

48. 使用block和使用delegate完成委托模式有什么优点?

	SDWebImageView最开始全是用Delegate来实现，因为2009年的时候还没有Block。后来换成block后，精减了很多的代码，但功能一点没少。所以，可以认为block更加精练。

49. 多线程与block

50. 谈谈Objective-C的内存管理方式及过程？
    
	iOS编译器会在我们的源代码中添加release, retain, autorelease等方法。也就是自动引用计数。当一个对象的引用计数为0时就会开始对其进行回收。

51. Object-C有私有方法吗？私有变量呢？

	个人觉得OC的字典里没有私有这个词。勉强可以理解为没有在`.h`文件中暴露的方法和变量都算私有吧。使用系统提供的KVC可以很方便的拿到所有变量，方法，只要你知道变量名和方法签名。

52. C和obj-c 如何混用
    
 	`.m`文件中可以直接写C代码。

53. ViewController的didReceiveMemoryWarning怎么被调用

	只能被系统调用。重写该方法，当内存不足时对内存进行释放。必须调用`super`

54. 什么时候用delegate,什么时候用Notification?

	通知是一对多，并且通知的发送方不关心谁来接收通知。进而不知道接收方的任何信息。接收方也不用声明自己有什么样的条件。

55. 关键字const有什么含意？修饰类呢? static的作用,用于类呢? 还有extern c的作用
    
    1.  变量的作用范围为该函数体，该变量的内存只被分配一次，因此其值在下次调用时仍维持上次的值；
    2.  在模块内的 static全局变量可以被模块内所用函数访问，但不能被模块外其它函数访问；
    3.  在模块内的 static函数只可被这一模块内的其它函数调用，这个函数的使用范围被限制在声明 它的模块内；
    4.  在类中的 static成员变量属于整个类所拥有，对类的所有对象只有一份拷贝；
    5.  在类中的 static 成员函数属于整个类所拥有，这个函数不接收 this 指针，因而只能访问类的static 成员变量

56. 关键字volatile有什么含意?并给出三个不同的例子。

	volatile: 当一个线程频繁的访问一个变量时，系统出于性能优化的考虑，将变量的值保存在寄存器中。下次直接从寄存器中读取。

	可是, 对于操作系统来说，寄存器不是线程共享的, 所以当有另外一个线程对变量进行访问的时候，变量的值就不对了。

57. 线程与进程的区别和联系?

58. 列举几种进程的同步机制，并比较其优缺点。

	呃，iOS有毛线的进程同步。

59. 进程之间通信的途径

60. 进程死锁的原因

61. 死锁的4个必要条件

	* 产生死锁的原因主要是：
	1. 因为系统资源不足。
	2. 进程运行推进的顺序不合适。
	3. 资源分配不当等。
	* 产生死锁的四个必要条件：
	1. 互斥条件：一个资源每次只能被一个进程使用。
	2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
	3. 不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺。
	4. 循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系。

62. 死锁的处理

63. cocoa touch框架	

64. 自动释放池是什么,如何工作

	MRC中，调用`[obj autorelease]`来延迟内存的释放是一件简单自然的事。
	
	ARC中编辑器自动为我们添加了这个。调用这个方法后变量被添加自动释放池中。详细的内容可以查看[黑幕背后的Autorelease](http://blog.sunnyxx.com/2014/10/15/behind-autorelease/)

65. Objective-C的优缺点。

    1. 优点当然是他的动态性。但这种动态性在一定程度上被Apple阉割了。
    2. oc没有包的概念。所以容易形成方法名重叠。开发过程中都要加前缀
    3. oc没有访问控制public private protect

66. sprintf, strcpy, memcpy使用上有什么要注意的地方。

67. http和Socket通信的区别。

	1. http是应用层协议（物理、数据链路、网络、传输、会话、表示、应用）
	2. socket是一个TCP/IP协议位于网络层
	3. socket为http提供连接基础

68. TCP和UDP的区别
    
	4. TCP提供面向连接的传输方式。可以理解为一个专有长连接
	5. UDP基于数据报的传输方式，涉及拆包，分不同的线路传输。收到后需要排序，合成等。网络不好会丢包。

69. 你了解svn,cvs等版本控制工具么？

	git

70. 什么是push?

	1. 远程推送：这里应该是推送通知NSNotification：APNS（Apple Push Notification Service 苹果推送通知服务）；
       1.  生成证书
       2.  注册通知
       3.  生成devicetoken并发送给服务端
       4.  服务端根据devicetoken将通知发送给APNS     
	1. 本地推送

71. 静态链接库

	1. `.a`
	3. `framework`
	4. 在编译的链接阶段将静态库的内容链接到目标文件中。替换了链接符号

72. 什么是沙盒模型？哪些操作是属于私有api范畴?

73. 在一个对象的方法里面：`self.name= “object”`和`_name =”object”`有什么不同吗?

74. 请简要说明viewDidLoad和viewDidUnload何时调用

	iOS6及以后不会再调用`viewDidUnload`了

75. 简述内存分区情况
	
	1. 代码区：用来存放函数、二进制代码及最静态的东西
	2. 数据区：系统运行时，申请内存并初始化，系统退出时，由系统释放。一般用来存放全局变量、静态变量、常量
	3. 堆区：通过malloc等函数或者new等操作动态申请得到，需要程序员手动申请或释放
	4. 栈区：函数模块内申请，函数结束时系统自动释放。存放局部变量、函数参数、结构体中创建的变量也在栈中
	
76. 队列和栈有什么区别：

77. HTTP协议中，POST和GET的区别是什么？

	主要出于安全考量

78. iOS的系统架构

79. 控件主要响应3种事件
    
	触摸事件,运动事件,远程控制；当view的alpha小于0.1，hidden为yes，不可交互时，不响应事件。

80. xib文件的构成分为哪3个图标？都具有什么功能

81. 简述视图控件器的生命周期

	1. alloc 创建对象，分配空间
	2. init (initWithNibName) 初始化对象，初始化数据
	3. loadView 从nib载入视图 ，通常这一步不需要去干涉。除非你没有使用xib文件创建视图
	4. viewDidLoad 载入完成，可以进行自定义数据以及动态创建其他控件
	5. viewWillAppear 视图将出现在屏幕之前，马上这个视图就会被展现在屏幕上了
	6. viewWillLayoutSubviews
	7. viewDidAppear 视图已在屏幕上渲染完成，当一个视图被移除屏幕并且销毁的时候的执行顺序，这个顺序差不多和上面的相反
	8. viewWillDisappear 视图将被从屏幕上移除之前执行
	9. viewDidDisappear 视图已经被从屏幕上移除，用户看不到这个视图了
	10. dealloc 视图被销毁，此处需要对你在init和viewDidLoad中创建的对象进行释放

82. 动画有基本类型有哪几种；表视图有哪几种基本样式?

83. 实现简单的表格显示需要设置UITableView的什么属性、实现什么协议？

84. Cocoa Touch提供了哪几种Core Animation过渡类型？

85. Quatrz 2D的绘图功能的三个核心概念是什么并简述其作用。

86. iPhone OS主要提供了几种播放音频的方法？

87. 使用AVAudioPlayer类调用哪个框架、使用步骤？

88. 有哪几种手势通知方法、写清楚方法名？

	```obj-c
	touchesBeganWithEvent:
	touchesEndedWithEvent:
	touchesMovedWithEvent:
	touchesBegan:withEvent:
	```

89. CFSocket使用有哪几个步骤。

90. Core Foundation中提供了哪几种操作Socket的方法？

91. 解析XML文件有哪几种方式？

	[解析xml的4种方法详解](http://blog.csdn.net/jzhf2012/article/details/8532873)

92. ios 平台怎么做数据的持久化?coredata 和sqlite有无必然联系？coredata是一个关系型数据库吗？

93. tableView 的重用机制？

94. 讲一下MVC和MVVM，MVP？

95. 为什么代理要用weak？代理的delegate和dataSource有什么区别？block和代理的区别?

96. 属性的实质是什么？包括哪几个部分？属性默认的关键字都有哪些？@dynamic关键字和@synthesize关键字是用来做什么的？

97. 属性的默认关键字是什么？

	基本数据：atomic, readwrite, assign
	普通的OC对象：atomic, readwrite, strong

98. NSString为什么要用copy关键字，如果用strong会有什么问题？

99. 如何令自己所写的对象具有拷贝功能?

100. 可变集合类 和 不可变集合类的 copy 和 mutablecopy有什么区别？如果是集合是内容复制的话，集合里面的元素也是内容复制么？

101. 为什么IBOutlet修饰的UIView也适用weak关键字？

102. nonatomic和atomic的区别？atomic是绝对的线程安全么？为什么？如果不是，那应该如何实现？

103. UICollectionView自定义layout如何实现？

104. 用StoryBoard开发界面有什么弊端？如何避免？

105. 进程和线程的区别？同步异步的区别？并行和并发的区别？

106. 线程间通信？

107. GCD的一些常用的函数？

108. 如何使用队列来避免资源抢夺？

109. 说一下appdelegate的几个方法？从后台到前台调用了哪些方法？第一次启动调用了哪些方法？从前台到后台调用了哪些方法？

		```
		info.plist
		load math-o
		dyld加载并初始化依赖库
		dyld执行程序可执行文件的init-> load方法
		dyld返回程序入口main
		didfinishedlaunchingwithoption
		didbecomeaction
		home
		willresignaction
		didenterbackground
		回到前台
		willengerforeground
		didbecomeaction
		...
		applicationwillteminal
		```

111. NSCache优于NSDictionary的几点？

		但是开发人员对NSCache基本不能操作，只能建议。所以YapDatabase没有使用NSCache进行缓存，而是使用自己创建的缓存

112. 知不知道Designated Initializer（NS_DESIGNATED_INITIALIZER）？使用它的时候有什么需要注意的问题

113. 实现description方法能取到什么效果？
    
		lldb po 的时候调用的是desc方法来打印细节，如果不实现这个方法，基本看不到自定义内的内容。所以这个方法就是用来调试的。

114. objc使用什么机制管理对象内存？

115. block的实质是什么？一共有几种block？都是什么情况下生成的？
    
		本质是一个函数指针

116. 为什么在默认情况下无法修改被block捕获的变量？`__block`都做了什么？
    
		栈区变成了红灯区，堆区变成了绿灯区。

117. objc在向一个对象发送消息时，发生了什么？

118. 什么时候会报unrecognized selector错误？iOS有哪些机制来避免走到这一步？
    
		```obj-c
		//方案一：动态方法解析
		-(BOOL)resolveInstanceMethod:(SEL)sel
		-(BOOL)resolveClassMethod:(SEL)sel
		//方案二：备援接收者
		-(id)forwardingTargetForSelector:(SEL)aSelector
		//方案三：完备消息转发
		-(NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector;
		-(void)forwardInvocation:(NSInvocation *)anInvocation;
		```

119. 能否向编译后得到的类中增加实例变量？能否向运行时创建的类中添加实例变量？为什么？
	
		objc_run_time

120. runtime如何实现weak变量的自动置nil？

		说是有一张表，使用内存地址作为key，以weak对象做为value。当内存地址的内容被释放了，对应的对象被置为Nil

121. 给类添加一个属性后，在类结构体里哪些元素会发生变化？

 		`ivar_list`, `method_list`, `propty_list`

122. runloop是来做什么的？runloop和线程有什么关系？主线程默认开启了runloop么？子线程呢？

123. runloop的mode是用来做什么的？有几种mode？

124. 为什么把NSTimer对象以NSDefaultRunLoopMode（kCFRunLoopDefaultMode）添加到主运行循环以后，滑动scrollview的时候NSTimer却不动了？

125. 苹果是如何实现Autorelease Pool的？

126. isa指针？（对象的isa，类对象的isa，元类的isa都要说）

127. 类方法和实例方法有什么区别？

128. 介绍一下分类，能用分类做什么？内部是如何实现的？它为什么会覆盖掉原来的方法？

129. 运行时能增加成员变量么？能增加属性么？如果能，如何增加？如果不能，为什么？

130. objc中向一个nil对象发送消息将会发生什么？（返回值是对象，是标量，结构体）

131. UITableview的优化方法?

		缓存高度，异步绘制，减少层级，hide，避免离屏渲染

132. 有没有用过运行时，用它都能做什么？（交换方法，创建类，给新创建的类增加方法，改变isa指针）

133. 看过哪些第三方框架的源码？都是如何实现的？（如果没有，问一下多图下载的设计）

134. AFN为什么添加一条常驻线程？

		为了提高性能。网络请求算是一种相当频繁的操作，如果每次都开一个线程，用完就销毁，这样频繁的操作会有性能问题。

135. KVO的使用？实现原理？（为什么要创建子类来实现）

136. KVC的使用？实现原理？（KVC拿到key以后，是如何赋值的？知不知道集合操作符，能不能访问私有属性，能不能直接访问_ivar）

137. 有已经上线的项目么？

138. 项目里哪个部分是你完成的？

139. 开发过程中遇到过什么困难，是如何解决的？

140. 遇到一个问题完全不能理解的时候，是如何帮助自己理解的？举个例子？

141. 有看书的习惯么？最近看的一本是什么书？有什么心得？

142. 有没有使用一些笔记软件？会在多平台同步以及多渠道采集么？（如果没有，问一下是如何复习知识的）

143. 有没有使用清单类，日历类的软件？（如果没有，问一下是如何安排，计划任务的）

144. 平常看博客么？有没有自己写过？（如果写，有哪些收获？如果没有写，问一下不写的原因）

145. category 和 extension 的区别

146. define 和 const常量有什么区别?

     	1. define在预处理阶段进行替换，const常量在编译阶段使用
     	2. 宏不做类型检查，仅仅进行替换，const常量有数据类型，会执行类型检查
     	3. define不能调试，const常量可以调试
     	4. define定义的常量在替换后运行过程中会不断地占用内存，而const定义的常量存储在数据段只有一份copy，效率更高
     	5. define可以定义一些简单的函数，const不可以

147. ARC通过什么方式帮助开发者管理内存？

		通过编译器在编译的时候,插入类似内存管理的代码

148. ARC是为了解决什么问题诞生的？

		总的来说就是MRC需要开发者耗费大量的精力去注意内存管理，降低了开发效率

149. ARC下还会存在内存泄露吗？

     	1. 循环引用会导致内存泄露
     	2. Objective-C对象与CoreFoundation对象进行桥接的时候如果管理不当也会造成内存泄露
     	3. CoreFoundation中的对象不受ARC管理，需要开发者手动释放   

150. 什么情况使用weak关键字，相比assign有什么不同？

		首先明白什么情况使用weak关键字？
		在ARC中,在有可能出现循环引用的时候,往往要通过让其中一端使用weak来解决,比如:delegate代理属性，代理属性也可使用assign自身已经对它进行一次强引用,没有必要再强引用一次,此时也会使用weak,自定义IBOutlet控件属性一般也使用weak；当然，也可以使用strong，但是建议使用weak
		weak 和 assign的不同点
		weak策略在属性所指的对象遭到摧毁时，系统会将weak修饰的属性对象的指针指向nil，在OC给nil发消息是不会有什么问题的；如果使用assign策略在属性所指的对象遭到摧毁时，属性对象指针还指向原来的对象，由于对象已经被销毁，这时候就产生了野指针，如果这时候在给此对象发送消息，很容造成程序奔溃
		assigin 可以用于修饰非OC对象,而weak必须用于OC对象

151. @property 的本质是什么？

		@property其实就是在编译阶段由编译器自动帮我们生成ivar成员变量，getter方法，setter方法

152. ivar、getter、setter是如何生成并添加到这个类中的？

		// 该属性的“偏移量” (offset)，这个偏移量是“硬编码” (hardcode)，表示该变量距离存放对象的内存区域的起始地址有多远
		OBJC_IVAR_$类名$属性名称
		// 方法对应的实现函数
		setter与getter
		// 成员变量列表
		ivar_list
		// 方法列表
		method_list
		// 属性列表
		prop_list

		每次增加一个属性，系统都会在`ivar_list`中添加一个成员变量的描述
		在`method_list`中增加setter与getter方法的描述
		在`prop_list`中增加一个属性的描述
		计算该属性在对象中的偏移量
		然后给出setter与getter方法对应的实现,在setter方法中从偏移量的位置开始赋值,在getter方法中从偏移量开始取值,为了能够读取正确字节数,系统对象偏移量的指针类型进行了类型强转

153. protocol 和 category 中如何使用 @property @synthesize 和@dynamic分别有什么作用

		@property有两个对应的词，一个是@synthesize，一个是@dynamic。如果@synthesize和@dynamic都没写，那么`默认`的就是`@syntheszie var = _var;`
		@synthesize的语义是如果你没有手动实现setter方法和getter方法，那么编译器会`自动`为你加上这两个方法
		@dynamic告诉编译器：属性的setter与getter方法由`用户自己实现`，不自动生成（当然对于readonly的属性只需提供getter即可）
		假如一个属性被声明为@dynamic var，然后你`没有`提供@setter方法和@getter方法，`编译的时候没问题`，但是当程序运行到instance.var = someVar，由于缺setter方法会导致程序崩溃；或者当运行到 someVar = instance.var时，由于缺getter方法同样会导致崩溃`编译时没问题，运行时才执行相应的方法，这就是所谓的动态绑定`

154. @synthesize合成实例变量的规则是什么？假如property名为foo，存在一个名为_foo的实例变量，那么还会自动合成新变量么？

		先回答第二个问题：不会
		@synthesize合成成员变量的规则，有以下几点：
		如果指定了成员变量的名称,会生成一个指定的名称的成员变量
		如果这个成员已经存在了就不再生成了
		如果指定@synthesize foo;就会生成一个名称为foo的成员变量，也就是说：会自动生成一个属性同名的成员变量

		```objc
		@interface XMGPerson : NSObject
		@property (nonatomic, assign) int age;
		@end
		@implementation XMGPerson
		// 不加这语句默认生成的成员变量名为_age
		// 如果加上这一句就会生成一个跟属性名同名的成员变量
		@synthesize age;
		@end
		```

		如果是`@synthesize foo = _foo;` 就不会生成成员变量了


155. 在有了自动合成属性实例变量之后，@synthesize还有哪些使用场景？

156. 怎么用 copy 关键字？

157. 用@property声明的NSString（或NSArray，NSDictionary）经常使用copy关键字，为什么？如果改用strong关键字，可能造成什么问题？

		在一次赋值操作完成以后，内部变量可能被外部修改，而外部变量也可能被内部修改。

158. 复制详解

		深、浅、完成复制（复制集合对象时需要遍历集合内对象）

159. 这个写法会出什么问题： `@property (copy) NSMutableArray *array;`

160. 如何让自定义类可以用 copy 修饰符？如何重写带 copy 关键字的 setter？

161. `+(void)load; +(void)initialize;`有什么用处？

162. Foundation对象与Core Foundation对象有什么区别

163. `addObserver:forKeyPath:options:context:`各个参数的作用分别是什么，observer中需要实现哪个方法才能获得KVO回调？

164. KVO内部实现原理

165. KVO是基于runtime机制实现的

		当某个类的属性对象第一次被观察时，系统就会在运行期动态地创建该类的一个派生类，在这个派生类中重写基类中任何被观察属性的setter 方法。派生类在被重写的setter方法内实现真正的通知机制.
		如果原类为Person，那么生成的派生类名为NSKVONotifying_Person
		每个类对象中都有一个isa指针指向当前类，当一个类对象的第一次被观察，那么系统会偷偷将isa指针指向动态生成的派生类，从而在给被监控属性赋值时执行的是派生类的setter方法
    
		键值观察通知依赖于NSObject 的两个方法: `willChangeValueForKey:`和 `didChangevlueForKey:`；在一个被观察属性发生改变之前， `willChangeValueForKey:` 一定会被调用，这就 会记录旧的值。而当改变发生后，`didChangeValueForKey:` 会被调用，继而 `observeValueForKey:ofObject:change:context:`也会被调用。
    
		补充：KVO的这套实现机制中苹果还偷偷重写了class方法，让我们误认为还是使用的当前类，从而达到隐藏生成的派生类

166. 如何手动触发一个value的KVO

167. 若一个类有实例变量`NSString *_foo`，调用`setValue:forKey:`时，是以`foo`还是`_foo`作为`key`？

168. KVC的keyPath中的集合运算符如何使用？

169. KVC和KVO的keyPath一定是属性么？

		可以是成员变量，但成员变量没有KVC变量检索规则。

170. 如何关闭默认的KVO的默认实现，并进入自定义的KVO实现？

171. Size Classes 具体使用
    
		sb中用于进行设备适配用的

1. loadView的作用？

2. IBOutlet连出来的视图属性为什么可以被设置成weak?

3. 沙盒目录结构是怎样的？各自用于那些场景？
	
	1. Application：存放程序源文件，上架前经过数字签名，上架后不可修改
	2. Documents：常用目录，iCloud备份目录，存放数据
	3. Library
		1. Caches：存放体积大又不需要备份的数据
		2. Preference：设置目录，iCloud会备份设置信息
	4. tmp：存放临时文件，不会被备份，而且这个文件下的数据有可能随时被清除的可能

4. pushViewController和presentViewController有什么区别

	`presentViewController:animated:completion:` 是UIViewController的方法
	`dismissViewControllerAnimated:completion:`
	`pushViewController:animated:` 是 UINavigationController 的方法
	`popViewControllerAnimated:`
	`showViewController:sender:`

1. 请简述UITableView的复用机制

2. 如何高性能的给 UIImageView 加个圆角?

	考察的离屏渲染
	不好的解决方案
	使用下面的方式会强制Core Animation提前渲染屏幕的离屏绘制, 而离屏绘制就会给性能带来负面影响，会有卡顿的现象出现
	```obj-c
	self.view.layer.cornerRadius = 5;
	self.view.layer.masksToBounds = YES;
	```
	正确的解决方案：使用绘图技术
	```obj-c
	- (UIImage *)circleImage
	{
	// NO代表透明
	UIGraphicsBeginImageContextWithOptions(self.size, NO, 0.0);

	// 获得上下文
	CGContextRef ctx = UIGraphicsGetCurrentContext();

	// 添加一个圆
	CGRect rect = CGRectMake(0, 0, self.size.width, self.size.height);
	CGContextAddEllipseInRect(ctx, rect);

	// 裁剪
	CGContextClip(ctx);

	// 将图片画上去
	[self drawInRect:rect];

	UIImage *image = UIGraphicsGetImageFromCurrentImageContext();

	// 关闭上下文
	UIGraphicsEndImageContext();

	return image;
	}
	```

	还有一种方案：使用了贝塞尔曲线"切割"个这个图片, 给UIImageView 添加了的圆角，其实也是通过绘图技术来实现的

	```obj-c
	UIImageView *imageView = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
	imageView.center = CGPointMake(200, 300);
	UIImage *anotherImage = [UIImage imageNamed:@"image"];
	UIGraphicsBeginImageContextWithOptions(imageView.bounds.size, NO, 1.0);
	[[UIBezierPath bezierPathWithRoundedRect:imageView.bounds
	cornerRadius:50] addClip];
	[anotherImage drawInRect:imageView.bounds];
	imageView.image = UIGraphicsGetImageFromCurrentImageContext();
	UIGraphicsEndImageContext();
	[self.view addSubview:imageView];
	```

1. 使用drawRect有什么影响？

2. 描述下SDWebImage里面给UIImageView加载图片的逻辑

3. 控制器的生命周期

	```obj-c
	// 自定义控制器view，这个方法只有实现了才会执行
	- (void)loadView
	{
	self.view = [[UIView alloc] init];
	self.view.backgroundColor = [UIColor orangeColor];
	}
	// view是懒加载，只要view加载完毕就调用这个方法
	- (void)viewDidLoad
	{
	[super viewDidLoad];

	NSLog(@"%s",__func__);
	}

	// view即将显示
	- (void)viewWillAppear:(BOOL)animated
	{
	[super viewWillAppear:animated];

	NSLog(@"%s",__func__);
	}
	// view即将开始布局子控件
	- (void)viewWillLayoutSubviews
	{
	[super viewWillLayoutSubviews];

	NSLog(@"%s",__func__);
	}
	// view已经完成子控件的布局
	- (void)viewDidLayoutSubviews
	{
	[super viewDidLayoutSubviews];

	NSLog(@"%s",__func__);
	}
	// view已经出现
	- (void)viewDidAppear:(BOOL)animated
	{
	[super viewDidAppear:animated];

	NSLog(@"%s",__func__);
	}
	// view即将消失
	- (void)viewWillDisappear:(BOOL)animated
	{
	[super viewWillDisappear:animated];

	NSLog(@"%s",__func__);
	}
	// view已经消失
	- (void)viewDidDisappear:(BOOL)animated
	{
	[super viewDidDisappear:animated];

	NSLog(@"%s",__func__);
	}
	// 收到内存警告
	- (void)didReceiveMemoryWarning
	{
	[super didReceiveMemoryWarning];

	NSLog(@"%s",__func__);
	}
	// 方法已过期，即将销毁view
	- (void)viewWillUnload
	{

	}
	// 方法已过期，已经销毁view
	- (void)viewDidUnload
	{

	}
	```

180. 如何进行iOS6、7的适配

2. 如何渲染UILabel的文字？

3. UIScrollView的contentSize能否在viewDidLoad中设置？

4. 触摸事件的传递

5. 事件响应者链

6. 核心动画里包含什么？

7. 如何使用核心动画？

8. runtime怎么添加属性、方法等

9. 是否可以把比较耗时的操作放在NSNotificationCenter中

10. runtime 如何实现 weak 属性

11. weak属性需要在dealloc中置nil么

12. 一个Objective-C对象如何进行内存布局？

13. 一个objc对象的isa的指针指向什么？有什么作用？

14. runtime如何通过selector找到对应的IMP地址？（分别考虑类方法和实例方法）

15. objc中的类方法和实例方法有什么本质区别和联系

16.  使用runtime Associate方法关联的对象，需要在主对象dealloc的时候释放么？

17.  `_objc_msgForward`函数是做什么的？直接调用它将会发生什么？

18.  能否向编译后得到的类中增加实例变量？能否向运行时创建的类中添加实例变量？为什么？

19.  以`+scheduledTimerWithTimeInterval...`的方式触发的timer，在滑动页面上的列表时，timer会暂定回调，为什么？如何解决？

20.  猜想runloop内部是如何实现的？

21.  不手动指定`autoreleasepool`的前提下，一个`autorealese`对象在什么时刻释放？（比如在一个vc的viewDidLoad中创建）

22.  苹果是如何实现`autoreleasepool`的？

23.  GCD的队列（`dispatch_queue_t`）分哪两种类型？背后的线程模型是什么样的？

24.  苹果为什么要废弃`dispatch_get_current_queue`？

25.  如何用GCD同步若干个异步调用？（如根据若干个url异步加载多张图片，然后在都下载完成后合成一张整图）

26. `dispatch_barrier_async`的作用是什么？

27. lldb（gdb）常用的调试命令？

	1. po：打印对象，会调用对象description方法。是print-object的简写
	2. expr：可以在调试时动态执行指定表达式，并将结果打印出来，很有用的命令
	3. print：也是打印命令，需要指定类型
	4. bt：打印调用堆栈，是thread backtrace的简写，加all可打印所有thread的堆栈
	5. br l：是breakpoint list的简写
	6. `watchpoint set -v _vname`

207. BAD_ACCESS在什么情况下出现？

29. 如何调试BAD_ACCESS错误

	XCODE打开zombie检查；使用xcode开发工具箱的zombie检查；

209. 简述下Objective-C中调用方法的过程（runtime）

2. 什么是method swizzling（俗称黑魔法）

3. objc中向一个对象发送消息`[obj foo]`和`objc_msgSend()`函数之间有什么关系？

	在编译期进行了转换

212. 什么时候会报unrecognized selector的异常？

2. 使用block时什么情况会发生引用循环，如何解决？

3. 在block内如何修改block外部变量？

4. 使用系统的某些block api（如UIView的block版本写动画时），是否也考虑循环引用问题？

	如果方法中的一些参数是 成员变量，那么可以造成循环引用

216. OC中常见的循环引用总结

2. 常用的设计模式

3. TCP和UDP有什么区别？

4. TCP的三次握手

5. 如何制作一个静态库/动态库？他们的区别是什么？

6. 一个lib包含了很多的架构，会打到最后的包里么？

	不会,如果lib中有armv7, armv7s, arm64, i386,x86\_64架构，而target architecture选择了armv7s,arm64，那么只会从lib中link指定的这两个架构的二进制代码，其他架构下的代码不会link到最终可执行文件中；反过来，一个lib需要在模拟器环境中正常link，也得包含i386架构的指令

	```sh
	//常用命令总结：
	// 使用lipo -info命令，查看指定库支持的架构，比如UIKit框架
	lipo -info UIKit.framework/UIKit
	// 想看的更详细的信息可以使用lipo -detailed_info
	lipo -detailed_info UIKit.framework/UIKit
	// 还可以使用file命令
	file UIKit.framework/UIKit
	// 合并MyLib-32.a和MyLib-64.a，可以使用lipo -create命令合并
	lipo -create MyLib-32.a MyLib-64.a -output MyLib.a
	```

222. 支持64-bit后程序包会变大么？

2. 用过Core Data 或者 SQLite吗？读写是分线程的吗？遇到过死锁没？如何解决的？

3. 请简单的介绍下APNS发送系统消息的机制

4. 开发常用的工具有哪些？

5. 你一般是怎么用 Instruments 的？

  	1. Leaks（泄漏）：一般的查看内存使用情况，检查泄漏的内存，并提供了所有活动的分配和泄漏模块的类对象分配统计信息以及内存地址历史记录；
  	2. Time Profiler（时间探查）：执行对系统的CPU上运行的进程低负载时间为基础采样。
  	3. Allocations（内存分配）：跟踪过程的匿名虚拟内存和堆的对象提供类名和可选保留/释放历史；
  	4. Activity Monitor（活动监视器）：显示器处理的CPU、内存和网络使用情况统计；
  	5. Blank（空模板）：创建一个空的模板，可以从Library库中添加其他模板；
  	6. Automation（自动化）：这个模板执行它模拟用户界面交互为IOS机应用从instrument启动的脚本；
  	7. Core Data：监测读取、缓存未命中、保存等操作，能直观显示是否保存次数远超实际需要。
  	8. Cocoa Layout：观察约束变化，找出布局代码的问题所在。
  	9. Network：跟踪 TCP / IP和 UDP / IP 连接。
  	10. Automations：创建和编辑测试脚本来自动化 iOS 应用的用户界面测试。

6. 如何实现单例，单例会有什么弊端？

7. APP上架后如何搜集错误信息？

8. 简答描述下你所理解的敏捷开发

9. ScrollView无限滚动

11.  创建指定线程数的线程池

		如果使用NSOperationQueue，有现成的接口可以设置

		```obj-c
		NSOperationQueue *queue = [[NSOperationQueue alloc]init]；
		queue.maxConcurrentOperationCount = 2;
		//添加Operation任务...
		```

		但如果使用GDC，则需要借助`dispatch_semaphore_t`

12.  iOS线程锁有哪些，性能如何，各锁使用场景

13.  atomic能保证线程安全吗？

		[关于atomic 和 nonatomic 有什么区别](./iOSer/关于atomic和nonatomic有什么区别.md)


14.  iOS设备获取唯一设备号

		https://www.jianshu.com/p/7ad22ca88b83
		大概的意思就是说,从iOS2.0到iOS5.0使用的系统提供的API,但是由于涉及到用户隐私,所以苹果官方将其关闭。这个iOS 6中提供了两个变量由于标识设备。但这两个变量在用户卸载重装的时候会发生改变。所以,现在统一使用UUID,并将这个UUID存储到keychain中

15.  如何使用runtime hook一个class的某个方法，又如何hook某个instance的方法？

		```obj-c
		Method fromMethod = class_getInstanceMethod([self class], @selector(viewDidLoad));
		Method toMethod = class_getInstanceMethod([self class], @selector(swizzlingViewDidLoad));
		/**
		* 我们在这里使用class_addMethod()函数对Method Swizzling做了一层验证，如果self没有实现被交换的方法，会导致失败。
		* 而且self没有交换的方法实现，但是父类有这个方法，这样就会调用父类的方法，结果就不是我们想要的结果了。
		* 所以我们在这里通过class_addMethod()的验证，如果self实现了这个方法，class_addMethod()函数将会返回NO，我们就可以对其进行交换了。
		*/
		if (!class_addMethod([self class], @selector(viewDidLoad), method_getImplementation(toMethod), method_getTypeEncoding(toMethod))) {
		method_exchangeImplementations(fromMethod, toMethod);
		}
		```





