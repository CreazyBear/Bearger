# Google Objective-C Style Guide
#程序员/翻译/谷歌OC代码规范

[Google Objective-C Style Guide | style guide](http://google.github.io/styleguide/objcguide.html)

> Objective-C是C的一个动态的、面向对象的扩展，它被设计成易于使用和阅读，同时支持复杂的面向对象设计。它是OS X和iOS应用程序的主要开发语言。  
> 苹果已经为Objective-C编写了一个非常好的、被广泛接受的Cocoa编码指南。除本指南外，还请阅读[Introduction to Coding Guidelines for Cocoa](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html#//apple_ref/doc/uid/10000146-SW1)。  
> 本文档的目的是描述应该用于iOS和OS X代码的Objective-C(和objective - c++)编码指南和实践。随着时间的推移，这些指导方针在其他项目和团队中得到了发展和验证。谷歌开发的开源项目符合本指南的要求。  
> 注意，本指南不是Objective-C教程。我们假设读者熟悉这种语言。如果您是Objective-C的新手或需要复习，请阅读[About Objective-C](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html)。  


## 原则
- - - -
### 面向读者，而不是作者
代码库通常具有更长的生命周期，阅读代码比编写代码花费更多的时间。我们明确地选择优化我们的普通软件工程师在代码库中阅读、维护和调试代码的体验，而不是简单地编写代码。例如，当代码片段中发生意外或不寻常的事情时，为读者留下文本提示是很有价值的。

### 一致
当样式指南允许多个选项时，最好选择一个选项，而不是混合使用多个选项。在整个代码库中始终使用一种风格可以让工程师关注其他(更重要的)问题。一致性还可以实现更好的自动化，因为一致的代码允许更高效地开发和操作格式化或重构代码的工具。在很多情况下，那些被认为是“一致的”的规则可以归结为“只挑一个，不用担心”;在这些问题上允许灵活性的潜在价值被让人们争论的代价所抵消。

### 与苹果sdk保持一致
与苹果SDKs使用Objective-C的方式的一致性具有价值，原因与我们代码库中的一致性相同。如果Objective-C特性解决了一个问题，这就是使用它的理由。然而，有时语言特性和习语是有缺陷的，或者只是设计的假设不是通用的。在这种情况下，约束或禁止语言特性或习语是合适的。

### 风格规则应该发挥作用
样式规则的好处必须大到足以让工程师记住它。好处是相对于没有规则的代码基来衡量的，因此，如果人们不太可能这样做，反对非常有害的实践的规则可能仍然会有一点好处。这一原则主要解释了我们没有的规则，而不是我们有的规则:例如，goto违背了以下许多原则，但由于其极其罕见而未被讨论。


## 例子
- - - -
Talk is easy, show me the code! 所以让我们从一个示例开始，该示例应该能够让您对样式、间距、命名等等有所了解。
下面是一个示例头文件，演示了@interface声明的正确注释和间距。
```obj-c
// GOOD:

#import <Foundation/Foundation.h>

@class Bar;

/**
 * A sample class demonstrating good Objective-C style. All interfaces,
 * categories, and protocols (read: all non-trivial top-level declarations
 * in a header) MUST be commented. Comments must also be adjacent to the
 * object they're documenting.
 */
@interface Foo : NSObject

/** The retained Bar. */
@property(nonatomic) Bar *bar;

/** The current drawing attributes. */
@property(nonatomic, copy) NSDictionary<NSString *, NSNumber *> *attributes;

/**
 * Convenience creation method.
 * See -initWithBar: for details about @c bar.
 *
 * @param bar The string for fooing.
 * @return An instance of Foo.
 */
+ (instancetype)fooWithBar:(Bar *)bar;

/**
 * Designated initializer.
 *
 * @param bar A string that represents a thing that does a thing.
 */
- (instancetype)initWithBar:(Bar *)bar;

/**
 * Does some work with @c blah.
 *
 * @param blah
 * @return YES if the work was completed; NO otherwise.
 */
- (BOOL)doWorkWithBlah:(NSString *)blah;

@end
```

一个示例源文件，演示了接口@implementation的正确注释和间距。
```obj-c
// GOOD:

#import "Shared/Util/Foo.h"

@implementation Foo {
  /** The string used for displaying "hi". */
  NSString *_string;
}

+ (instancetype)fooWithBar:(Bar *)bar {
  return [[self alloc] initWithBar:bar];
}

- (instancetype)init {
  // Classes with a custom designated initializer should always override
  // the superclass's designated initializer.
  return [self initWithBar:nil];
}

- (instancetype)initWithBar:(Bar *)bar {
  self = [super init];
  if (self) {
    _bar = [bar copy];
    _string = [[NSString alloc] initWithFormat:@"hi %d", 3];
    _attributes = @{
      @"color" : [UIColor blueColor],
      @"hidden" : @NO
    };
  }
  return self;
}

- (BOOL)doWorkWithBlah:(NSString *)blah {
  // Work should be done here.
  return NO;
}

@end
```


## 间距和格式
- - - -
### 空格 VS Tab
只使用空格，每次缩进两个空格。我们使用空格来缩进。不要在代码中使用Tab。
您应该在按tab键时将编辑器设置为发出空格，并对行尾空格进行修剪。

### 行宽
Objective-C文件的最大行长度是100列。
通过在Xcode的第100列启用Preferences >文本编辑>页面指南，您可以更容易地发现违规。

### 方法声明和定义
在`-` or `+`和返回类型之间应该使用一个空格，除了参数之间之外，参数列表中不应该有空格。
方法应该是这样的:
```obj-c
// GOOD:
- (void)doSomethingWithString:(NSString *)theString {
  ...
}
```
星号前面的间距是可选的。添加新代码时，要与周围文件的样式一致。
如果您有太多的参数无法在一行中匹配，那么最好给每个参数一个自己的行。如果使用了多行，请在参数前面使用冒号对齐每个行。
```obj-c
// GOOD:

- (void)doSomethingWithFoo:(GTMFoo *)theFoo
                      rect:(NSRect)theRect
                  interval:(float)theInterval {
  ...
}
```

当第二个或更高的参数名称比第一个长时，至少将第二行和后面的行缩进四个空格，以保持冒号对齐:
```obj-c
// GOOD:

- (void)short:(GTMFoo *)theFoo
          longKeyword:(NSRect)theRect
    evenLongerKeyword:(float)theInterval
                error:(NSError **)theError {
  ...
}
```

### 条件判断
包含if、while、for和switch等比较运算符之后的空格。
```obj-c
// GOOD:

for (int i = 0; i < 5; ++i) {
}

while (test) {};
```


```obj-c
// AVOID:

if (hasSillyName)
  LaughOutLoud();               // AVOID.

for (int i = 0; i < 10; i++)
  BlowTheHorn();                // AVOID.
```

如果If子句有else子句，两个子句都应该使用大括号。
```obj-c
// GOOD:

if (hasBaz) {
  foo();
} else {
  bar();
}

// AVOID:

if (hasBaz) foo();
else bar();        // AVOID.

if (hasBaz) {
  foo();
} else bar();      // AVOID.
```

除非在下一个case之前没有插入代码，否则应该用注释记录到下一个case中。
```obj-c
// GOOD:

switch (i) {
  case 1:
    ...
    break;
  case 2:
    j++;
    // Falls through.
  case 3: {
    int k;
    ...
    break;
  }
  case 4:
  case 5:
  case 6: break;
}
```

### 表达式
在二进制运算符和赋值周围使用空格。省略一元运算符的空格。不要在括号内添加空格。
```obj-c
// GOOD:

x = 0;
v = w * x + y / z;
v = -y * (x + z);
```

表达式中的因素可能会省略空格。
```obj-c
// GOOD:

v = w*x + y/z;
```

### 方法调用
方法调用的格式应该类似于方法声明。

当可以选择格式样式时，请遵循在给定源文件中已经使用的约定。调用应该在一行中包含所有参数:
```obj-c
// GOOD:

[myObject doFooWith:arg1 name:arg2 error:arg3];
```

或者每行有一个参数，冒号对齐:
```obj-c
// GOOD:

[myObject doFooWith:arg1
               name:arg2
              error:arg3];
```

不要使用这些风格:
```obj-c
// AVOID:

[myObject doFooWith:arg1 name:arg2  // some lines with >1 arg
              error:arg3];

[myObject doFooWith:arg1
               name:arg2 error:arg3];

[myObject doFooWith:arg1
          name:arg2  // aligning keywords instead of colons
          error:arg3];
```


与声明和定义一样，当第一个关键字比其他关键字短时，将后面的行缩进至少4个空格，以保持冒号对齐:
```obj-c
// GOOD:

[myObj short:arg1
          longKeyword:arg2
    evenLongerKeyword:arg3
                error:arg4];
```

包含多个内联块的调用的参数名可以左对齐到四个空格缩进。

### 函数调用
函数调用应该包含与每行匹配的尽可能多的参数，除非需要更短的行来清楚说明或记录参数。
函数参数的延续行可以缩进以与开头括号对齐，也可以有四个空格缩进。
```obj-c
// GOOD:

CFArrayRef array = CFArrayCreate(kCFAllocatorDefault, objects, numberOfObjects,
                                 &kCFTypeArrayCallBacks);

NSString *string = NSLocalizedStringWithDefaultValue(@"FEET", @"DistanceTable",
    resourceBundle,  @"%@ feet", @"Distance for multiple feet");

UpdateTally(scores[x] * y + bases[x],  // Score heuristic.
            x, y, z);

TransformImage(image,
               x1, x2, x3,
               y1, y2, y3,
               z1, z2, z3);
```
使用具有描述性名称的局部变量来缩短函数调用和减少调用嵌套。
```obj-c
// GOOD:

double scoreHeuristic = scores[x] * y + bases[x];
UpdateTally(scoreHeuristic, x, y, z);
```

### 异常
@catch和@finally的格式异常与前面的}在同一行上。在@标签和左括号({)之间，以及@catch和已捕获对象声明之间添加一个空格。如果必须使用Objective-C异常，请按以下格式格式化它们。但是，请参阅避免抛出异常，因为您不应该使用异常。
```obj-c
// GOOD:

@try {
  foo();
} @catch (NSException *ex) {
  bar(ex);
} @finally {
  baz();
}
```

### 方法长度
喜欢小而集中的功能。
长函数和方法偶尔是合适的，因此对函数长度没有硬性限制。如果一个函数超过40行，考虑它是否可以在不损害程序结构的情况下被分解。
即使你的长函数现在工作得很好，几个月后有人修改它可能会添加新的行为。这可能导致难以找到的bug。保持你的函数简短，使别人更容易阅读和修改你的代码。
在更新遗留代码时，还要考虑将长函数分解为更小、更易于管理的部分。

### 行间距
有节制的使用垂直空格。
为了让屏幕上更容易地看到更多的代码，请避免将空行放在函数的大括号中。
将函数之间和逻辑代码组之间的空白行限制为一到两行。


## 命名
- - - -
在合理的范围内，名称应该尽可能具有描述性。遵循标准的Objective-C命名规则。
避免非标准的缩写。不要担心节省水平空间，因为让新读者立即理解您的代码要重要得多。例如:
```obj-c
// GOOD:

// Good names.
int numberOfErrors = 0;
int completedConnectionsCount = 0;
tickets = [[NSMutableArray alloc] init];
userInfo = [someObject object];
port = [network port];
NSDate *gAppLaunchDate;


// AVOID:

// Names to avoid.
int w;
int nerr;
int nCompConns;
tix = [[NSMutableArray alloc] init];
obj = [someObject object];
p = [network port];
```

任何类、类别、方法、函数或变量名都应该使用名称中的首字母和首字母大写。这遵循了苹果的标准，即在首字母缩略词(如URL、ID、TIFF和EXIF)的名称中使用所有大写字母。
C函数和typedef名称应该大写，并使用驼峰大小写，以适合于周围的代码。

### 文件名
文件名应该反映它们所包含的类实现的名称—包括大小写。
遵循项目使用的约定。文件扩展名应如下:
```
.h		C/C++/Objective-C header file
.m		Objective-C implementation file
.mm	Objective-C++ implementation file
.cc	Pure C++ implementation file
.c		C implementation file

```

包含可以跨项目共享或在大型项目中使用的代码的文件应该有一个明确的惟一名称，通常包括项目或类前缀。
类别的文件名应该包括要扩展的类的名称，比如`GTMNSString+Utils.h`或`NSTextView + GTMAutocomplete.h`

### 类名
类名(以及类别和协议名)应该以大写开头，并用混合大小写分隔单词。
在设计跨多个应用程序共享的代码时，前缀是可以接受和推荐的(例如GTMSendMessage)。对于依赖于外部库的大型应用程序类，还建议使用前缀。

### 分类名
类别名称应以3个字符前缀开头，以标识类别为项目的一部分或开放给一般使用。

类别名称应该包含它所扩展的类的名称。例如，如果我们想在NSString上创建一个用于解析的类别，我们会将该类别放在一个名为`NSString+ gtmparse.h`的文件中，该类别本身将命名为`GTMNSStringParsingAdditions`。文件名和类别可能不匹配，因为该文件可能有许多与解析相关的独立类别。该类别中的方法应该共享前缀`(gtm_myCategoryMethodOnAString:)`，以防止Objective-C的全局命名空间发生冲突。

类名和类别的左括号之间应该有一个空格。
```obj-c
// GOOD:

/** A category that adds parsing functionality to NSString. */
@interface NSString (GTMNSStringParsingAdditions)
- (NSString *)gtm_parsedString;
@end
```

### 方法名
方法和参数名通常以小写开头，然后使用混合大小写。
正确的大写应该被尊重，包括名字的开头。
```
// GOOD:

+ (NSURL *)URLWithString:(NSString *)URLString;
```

如果可能的话，方法名应该读起来像一个句子，这意味着您应该选择与方法名一起流动的参数名。Objective-C方法的名称往往很长，但这样做的好处是，一段代码几乎可以读起来像散文，因此很多实现注释都是不必要的。
在第二个和以后的参数名中只在必要时使用介词和连词，如“with”、“from”和“to”，以阐明方法的含义或行为。
```obj-c
// GOOD:

- (void)addTarget:(id)target action:(SEL)action;                          // GOOD; no conjunction needed
- (CGPoint)convertPoint:(CGPoint)point fromView:(UIView *)view;           // GOOD; conjunction clarifies parameter
- (void)replaceCharactersInRange:(NSRange)aRange
            withAttributedString:(NSAttributedString *)attributedString;  // GOOD.
```

返回对象的方法应该有一个以名词开头的名称，该名称表示返回的对象:
```
// GOOD:

- (Sandwich *)sandwich;      // GOOD.
// AVOID:

- (Sandwich *)makeSandwich;  // AVOID.
```

访问器方法的名称应该与其获取的对象相同，但不应该以get为前缀。例如:
```obj-c
// GOOD:

- (id)delegate;     // GOOD.

// AVOID:

- (id)getDelegate;  // AVOID.
```

返回布尔形容词值的访问器的方法名以is开头，但这些方法的属性名省略了is。点符号只用于属性名，而不用于方法名。
```obj-c
// GOOD:

@property(nonatomic, getter=isGlorious) BOOL glorious;
- (BOOL)isGlorious;

BOOL isGood = object.glorious;      // GOOD.
BOOL isGood = [object isGlorious];  // GOOD.
// AVOID:

BOOL isGood = object.isGlorious;    // AVOID.
// GOOD:

NSArray<Frog *> *frogs = [NSArray<Frog *> arrayWithObject:frog];
NSEnumerator *enumerator = [frogs reverseObjectEnumerator];  // GOOD.
// AVOID:

NSEnumerator *enumerator = frogs.reverseObjectEnumerator;    // AVOID.
```

有关Objective-C命名的更多细节，请参阅苹果命名方法指南。
这些指南仅适用于Objective-C方法。c++方法名继续遵循c++样式指南中设置的规则。

### 函数名
函数的命名有多种情况
通常，函数应该以大写字母开头，每个新单词(也就是“Camel Case”或“Pascal Case”)都应该有一个大写字母。
```obj-c
// GOOD:

static void AddTableEntry(NSString *tableEntry);
static BOOL DeleteFile(char *filename);
```

由于Objective-C不提供名称空间，所以非静态函数应该有一个前缀，以最小化名称冲突的机会。
```obj-c
// GOOD:

extern NSTimeZone *GTMGetDefaultTimeZone();
extern NSString *GTMGetURLScheme(NSURL *URL);
```

### 变量名
变量名通常以小写开头，并用混合大小写来分隔单词。

实例变量有前导下划线。文件作用域或全局变量有一个前缀g。例如:myLocalVariable， _myInstanceVariable, gMyGlobalVariable。

#### 常见的变量名

读者应该能够从名称推断出变量类型，但是不要对语法属性使用匈牙利符号，例如变量的静态类型(int或指针)。
在方法或函数的作用域之外声明的文件作用域或全局变量(而不是常量)应该很少，并且应该具有前缀g。
```obj-c
// GOOD:

static int gGlobalCounter;
```

#### 实例变量
实例变量名是混合大小写的，应该以下划线作为前缀，比如_usernameTextField。
注意:谷歌之前对Objective-C ivars的约定是后划线。现有项目可以选择在新代码中继续使用拖尾下划线，以保持项目代码库中的一致性。在每个类中应该保持前缀或后缀下划线的一致性。

#### 常量
常量符号(常量全局变量和静态变量以及用#define创建的常量)应该使用混合大小写来划分单词。
全局和文件作用域常量应该具有适当的前缀。
```obj-c
// GOOD:

extern NSString *const GTLServiceErrorDomain;

typedef NS_ENUM(NSInteger, GTLServiceError) {
  GTLServiceErrorQueryResultMissing = -3000,
  GTLServiceErrorWaitTimedOut       = -3001,
};
```

由于Objective-C不提供名称空间，带有外部链接的常量应该有一个前缀，以尽量减少名称冲突的机会，通常像ClassNameConstantName或ClassNameEnumName。
为了与Swift代码互操作性，枚举值应该具有扩展typedef名称的名称:
```obj-c
// GOOD:

typedef NS_ENUM(NSInteger, DisplayTinge) {
  DisplayTingeGreen = 1,
  DisplayTingeBlue = 2,
};
```
常量可以在适当的时候使用小写的k前缀:
```obj-c
// GOOD:

static const int kFileCount = 12;
static NSString *const kUserKey = @"kUserKey";
```


## 类型和声明
- - - -
### 局部变量
在最狭窄的实用范围内声明变量，并接近它们的使用。在它们的声明中初始化变量。
```obj-c
// GOOD:

CLLocation *location = [self lastKnownLocation];
for (int meters = 1; meters < 10; meters++) {
  reportFrogsWithinRadius(location, meters);
}
```

有时，效率会使在其使用范围之外声明变量更为合适。这个示例声明了与初始化分离的米，并在每次循环中发送lastKnownLocation消息:
```obj-c
// AVOID:

int meters;                                         // AVOID.
for (meters = 1; meters < 10; meters++) {
  CLLocation *location = [self lastKnownLocation];  // AVOID.
  reportFrogsWithinRadius(location, meters);
}
```

在自动引用计数中，指向Objective-C对象的指针默认初始化为nil，因此不需要显式地初始化为nil。

### 无符号整数
避免无符号整数，除非匹配系统接口使用的类型。

当使用无符号整数进行数学运算或倒数为零时，会出现一些细微的错误。除了在系统接口中匹配NSUInteger外，在数学表达式中只依赖有符号整数。
```obj-c
// GOOD:

NSUInteger numberOfObjects = array.count;
for (NSInteger counter = numberOfObjects - 1; counter > 0; --counter)
// AVOID:

for (NSUInteger counter = numberOfObjects - 1; counter > 0; --counter)  // AVOID.
```

无符号整数可能用于标志和位掩码，但NS_OPTIONS或NS_ENUM通常更合适。

### 内存大小可变的类型

由于大小在32位和64位构建中有所不同，所以除了匹配系统接口外，请避免使用long、NSInteger、NSUInteger和CGFloat。
long、NSInteger、NSUInteger和CGFloat在32位和64位构建之间大小不同。在处理由系统接口公开的值时，使用这些类型是合适的，但是对于大多数其他计算，应该避免使用这些类型。
```obj-c
// GOOD:

int32_t scalar1 = proto.intValue;

int64_t scalar2 = proto.longValue;

NSUInteger numberOfObjects = array.count;

CGFloat offset = view.bounds.origin.x;

// AVOID:

NSInteger scalar2 = proto.longValue;  // AVOID.
```

文件和缓冲区大小通常超过32位的限制，因此应该使用int64_t声明它们，而不是使用long、NSInteger或NSUInteger。


## 注释
- - - -
注释对于保持代码的可读性至关重要。下面的规则描述了你应该评论什么和在哪里。但是请记住:虽然注释很重要，但是最好的代码是自文档化的。为类型和变量提供合理的名称要比使用晦涩的名称然后通过注释来解释要好得多。

注意标点、拼写和语法;写得好的评论比写得不好的评论更容易阅读。

注释应该和叙述性文本一样具有可读性，适当的大小写和标点符号。在许多情况下，完整的句子比句子片段更容易读懂。较短的注释，例如一行代码末尾的注释，有时可能不太正式，但使用一致的风格。当你写评论的时候，为你的读者写:下一个需要理解你的代码的贡献者。慷慨大方——下一个可能是你!

### 文件注释
文件可以选择以其内容的描述开始。每个文件可能包含以下项目，以便:
1. 许可样板如果必要的话。为项目使用的许可证选择适当的样板文件。
2. 必要时对文件内容的基本描述。
如果您使用作者行对文件进行了重大更改，请考虑删除作者行，因为修订历史已经提供了更详细和准确的作者记录。

### 声明的注释
每一个重要的接口，无论是公有的还是私有的，都应该有一个附带的注释来描述它的目的以及它如何融入更大的接口。
注释应该用于记录类、属性、ivars、函数、类别、协议声明和枚举。
```obj-c
// GOOD:

/**
 * A delegate for NSApplication to handle notifications about app
 * launch and shutdown. Owned by the main app controller.
 */
@interface MyAppDelegate : NSObject {
  /**
   * The background task in progress, if any. This is initialized
   * to the value UIBackgroundTaskInvalid.
   */
  UIBackgroundTaskIdentifier _backgroundTaskID;
}

/** The factory that creates and manages fetchers for the app. */
@property(nonatomic) GTMSessionFetcherService *fetcherService;

@end
```

在Xcode解析接口以显示格式化文档时，鼓励使用doxygenstyle注释。有各种各样的脱氧土霉素命令;在项目中一致地使用它们。
如果您已经在文件顶部的注释中详细描述了接口，那么您可以简单地声明:“查看文件顶部的注释以获得完整的描述”，但是一定要有某种注释。
此外，每个方法都应该有一个注释来解释它的函数、参数、返回值、线程或队列假设以及任何副作用。文档注释应该在公共方法的标题中，或者在非普通私有方法的方法前面。
对于方法和函数注释，使用描述性表单(“打开文件”)而不是命令式表单(“打开文件”)。注释描述了函数;它没有告诉函数要做什么。
记录类、属性或方法所做的线程使用假设(如果有的话)。如果类的一个实例可以被多个线程访问，那么要特别注意记录多线程使用的规则和不变量。
属性和ivars的前哨值(如NULL或-1)应该在注释中记录。
声明注释解释了如何使用方法或函数。说明如何实现方法或函数的注释应该与实现一起使用，而不是与声明一起使用。

### 实现的注释
提供注释来解释复杂、微妙或复杂的代码部分。
```obj-c
// GOOD:

// Set the property to nil before invoking the completion handler to
// avoid the risk of reentrancy leading to the callback being
// invoked again.
CompletionHandler handler = self.completionHandler;
self.completionHandler = nil;
handler();
```

如果有用，还可以提供关于考虑或放弃的实现方法的评论。
行尾注释应该与代码分隔至少2个空格。如果您对后面的行有几个注释，那么将它们对齐通常会更容易阅读。
```obj-c
// GOOD:

[self doSomethingWithALongName];  // Two spaces before the comment.
[self doSomethingShort];          // More spacing to align the comment.
```

### 二义性消除符号
在需要避免歧义的地方，使用反勾或竖线在注释中引用变量名和符号，而不是使用引号或内联符号命名。
在doxygenstyle注释中，更喜欢用单空间文本命令(比如@c)来标定符号。
当一个符号是一个常见的词，可能会让句子读起来像结构糟糕的时候，分界有助于提供清晰的含义。一个常见的例子是符号计数:
```obj-c
// GOOD:

// Sometimes `count` will be less than zero.
```

或者在引用已经包含引用的内容时
```obj-c
// GOOD:

// Remember to call `StringWithoutSpaces("foo bar baz")`
```

当一个符号是自可见的时，不需要反勾或竖线。
```obj-c
// GOOD:

// This class serves as a delegate to GTMDepthCharge.
```

Doxygen格式化也适用于标识符号。
```obj-c
// GOOD:

/** @param maximum The highest value for @c count. */
```

### 对象所有权
对于不受ARC管理的对象，当指针所有权模型不属于最常见的Objective-C用法习惯用法时，应使它尽可能明确。

#### 手动引用计数
假设`nsobject`派生对象的实例变量被保留;如果它们没有被保留，它们应该被注释为弱，或者用`__weak`限定符声明。
Mac软件中的实例变量标记为`@IBOutlets`，假设这些变量不被保留。
当实例变量是指向Core Foundation、c++和其他非objective -C对象的指针时，它们应该总是用强注释和弱注释来声明，以指示哪些指针是保留的，哪些指针是不保留的。Core Foundation和其他非objective - c对象指针需要显式内存管理，即使是在构建自动引用计数时也是如此。
强和弱声明的例子:
```obj-c
// GOOD:

@interface MyDelegate : NSObject

@property(nonatomic) NSString *doohickey;
@property(nonatomic, weak) NSString *parent;

@end


@implementation MyDelegate {
  IBOutlet NSButton *_okButton;  // Normal NSControl; implicitly weak on Mac only

  AnObjcObject *_doohickey;  // My doohickey
  __weak MyObjcParent *_parent;  // To send messages back (owns this instance)

  // non-NSObject pointers...
  CWackyCPPClass *_wacky;  // Strong, some cross-platform object
  CFDictionaryRef *_dict;  // Strong
}
@end

```

#### 自动引用计数
在使用ARC时，对象的所有权和生存期是显式的，因此对于自动保留的对象不需要额外的注释。


## C语言特性
- - - -
### 宏
避免使用宏，特别是使用常量变量、枚举、XCode片段或C函数。

宏使您看到的代码与编译器看到的代码不同。现代C使得传统的常量宏和实用函数的使用变得不必要。宏应该只在没有其他可用解决方案时使用。

当需要宏时，使用唯一的名称来避免编译单元中符号冲突的风险。如果可行的话，在使用宏之后，将范围限制在# undefine这个宏。

宏名称应该使用shouty_snake_case -所有大写字母，单词之间有下划线。类函数宏可以使用C函数命名实践。不要定义看起来是C或Objective-C关键字的宏。
```obj-c
// GOOD:

#define GTM_EXPERIMENTAL_BUILD ...      // GOOD

// Assert unless X > Y
#define GTM_ASSERT_GT(X, Y) ...         // GOOD, macro style.

// Assert unless X > Y
#define GTMAssertGreaterThan(X, Y) ...  // GOOD, function style.

// AVOID:

#define kIsExperimentalBuild ...        // AVOID

#define unless(X) if(!(X))              // AVOID
```

避免宏扩展到不平衡的C或Objective-C结构。避免引入范围的宏，或者可能会模糊块中值的捕获。

避免将在头文件中生成类、属性或方法定义的宏用作公共API。这只会使代码难以理解，而且这种语言已经有了更好的方式来做到这一点。

避免生成方法实现或生成变量声明的宏，这些变量稍后将在宏之外使用。宏不应该通过隐藏变量声明的位置和方式使代码难以理解。
```obj-c
// AVOID:

#define ARRAY_ADDER(CLASS) \
  -(void)add ## CLASS ## :(CLASS *)obj toArray:(NSMutableArray *)array

ARRAY_ADDER(NSString) {
  if (array.count > 5) {              // AVOID -- where is 'array' defined?
    ...
  }
}
```

可接受的宏使用的例子包括断言和调试日志宏，它们是基于构建设置有条件地编译的——通常，这些宏不会编译到发布版本中。


### 非标准扩展
除非另有说明，否则不能使用C/Objective-C的非标准扩展。

编译器支持不属于标准c的各种扩展，例如复合语句表达式`foo = ({ int x; Bar(&x); x })`和可变长度数组。

`__attribute__`是一个已批准的异常，因为它在Objective-C API规范中使用。

条件运算符的二进制形式`A ?: B`是一个被批准的特例。

## Cocoa and Objective-C 特性
- - - -
### 指定初始化函数
清楚地识别指定的初始化器。

对于那些可能子类化类的人来说，明确指定的初始化器是很重要的。这样，他们只需要覆盖一个初始化器(可能是几个初始化器)就可以保证他们子类的初始化器被调用。它还帮助那些将来调试您的类的人理解初始化代码流，如果他们需要一步完成它的话。使用注释或NS_DESIGNATED_INITIALIZER宏来标识指定的初始化器。如果使用NS_DESIGNATED_INITIALIZER，请用NS_UNAVAILABLE标记不支持的初始化程序。

### 重写初始化函数
当编写一个需要`init`方法的子类时，请确保覆盖超类的指定初始化器。

如果您没有覆盖超类的指定初始化器，那么您的初始化器可能在所有情况下都不会被调用，从而导致难以察觉和很难找到bug。


### 覆盖NSObject方法
将NSObject的重写方法放在@implementation的顶部。

这通常适用于(但不限于)init…、copyWithZone:和dealloc方法。初始化…方法应该分组在一起，然后是其他典型的NSObject方法，如description, isEqual:和hash。

用于创建实例的方便类工厂方法可以先于NSObject方法。

### 初始化函数
在init方法中，不要将实例变量初始化为0或nil;这样做是多余的。

新分配对象的所有实例变量都被初始化为0 (isa除外)，所以不要通过重新初始化变量为0或nil来打乱init方法。
（译者注：但在实际开发过程中，有遇到一种bug，一个整形或者浮点型变量，在没有被初始化的时候，值可能不为0）

### 头中的实例变量应该是@protected或@private
略

### 避免使用 +new
不要调用NSObject类方法new，也不要在子类中重写它。相反，使用alloc和init方法实例化保留的对象。

现代Objective-C代码显式地调用alloc和init方法来创建和保留对象。由于很少使用`new`类方法，这使得检查用于正确内存管理的代码变得更加困难。
(译者注：不理解)

### 保持公共API简单
保持简单的类;避免“洗碗槽”api。如果一个方法不需要是公共的，那么就让它远离公共接口。

与c++不同，Objective-C不区分公共方法和私有方法;任何消息都可以发送到对象。因此，避免将方法放置在公共API中，除非该类的使用者实际希望使用这些方法。这有助于减少在你没有预料到的情况下被调用的可能性。这包括从父类重写的方法。

由于内部方法并不是真正私有的，所以很容易意外地覆盖超类的“私有”方法，从而导致很难清除的bug。通常，私有方法应该有一个非常独特的名称，可以防止子类无意中重写它们。

### `#import and #include`
导入Objective-C和objective - c++头文件，`#include C/C++`头文件。

在`#import和#include`之间进行选择，这取决于您所包含的头的语言。

当包含使用Objective-C或objective - c++的头文件时，使用`#import`。当包含标准的C或c++头文件时，使用`#include`。头文件应该提供自己的`#define`保护。

### 包含头文件的顺序
标头包含的标准顺序是相关标头、操作系统标头、语言库标头，最后是其他依赖项的标头组。

相关的标头位于其他标头之前，以确保它没有隐藏的依赖项。对于实现文件，相关的头文件是头文件。对于测试文件，相关的标头是包含测试接口的标头。

空行可以分隔逻辑上不同的包含头的组。

使用相对于项目源目录的路径导入标题。
```
// GOOD:

#import "ProjectX/BazViewController.h"

#import <Foundation/Foundation.h>

#include <unistd.h>
#include <vector>

#include "base/basictypes.h"
#include "base/integral_types.h"
#include "util/math/mathutil.h"

#import "ProjectX/BazModel.h"
#import "Shared/Util/Foo.h"
```

### 对系统框架使用伞形标头
导入系统框架和系统库的伞形标头，而不是包含单个文件。

虽然从Cocoa或Foundation这样的框架中包含单个系统头看起来很诱人，但实际上如果包含顶级根框架，对编译器的工作就会更少。根框架通常是预编译的，可以更快地加载。另外，记得在Objective-C框架中使用`@import`或`#import`，而不是`#include`。
```obj-c
// GOOD:

@import UIKit;     // GOOD.
#import <Foundation/Foundation.h>     // GOOD.

// AVOID:

#import <Foundation/NSArray.h>        // AVOID.
#import <Foundation/NSString.h>
...
```

### 避免在初始化器和-dealloc中传递当前对象
初始化器和`-dealloc`中的代码应该避免调用实例方法。

超类初始化在子类初始化之前完成。在所有类都有机会初始化它们的实例状态之前，任何对self的方法调用都可能导致对未初始化的实例状态进行操作的子类。

对于`-dealloc`也存在类似的问题，方法调用可能导致类对已dealloc的状态进行操作。

一个不太明显的例子是属性访问器。这些可以像任何其他选择器一样被覆盖。只要可行，就直接在初始化器和-dealloc中分配和释放ivars，而不是依赖访问器。
```obj-c
// GOOD:

- (instancetype)init {
  self = [super init];
  if (self) {
    _bar = 23;  // GOOD.
  }
  return self;
}
```

小心将通用初始化代码分解为助手方法:

1. 方法可以在子类中被重写，可以是故意的，也可以是由于命名冲突而意外的。
2. 在编辑助手方法时，可能不清楚代码是从初始化器运行的。
```obj-c
// AVOID:

- (instancetype)init {
  self = [super init];
  if (self) {
    self.bar = 23;  // AVOID.
    [self sharedMethod];  // AVOID. Fragile to subclassing or future extension.
  }
  return self;
}

// GOOD:

- (void)dealloc {
  [_notifier removeObserver:self];  // GOOD.
}
// AVOID:

- (void)dealloc {
  [self removeNotifications];  // AVOID.
}
```

### NSString使用copy来修饰
接收NSString的setter总是应该复制它接受的字符串。这通常也适用于NSArray和NSDictionary等集合。

永远不要只保留字符串，因为它可能是NSMutableString。这样就避免了在你不知情的情况下，调用者对它进行了修改。

接收和保存集合对象的代码还应该考虑传递的集合可能是可变的，因此集合可以更安全地作为原始集合的副本或可变副本保存。
```obj-c
// GOOD:

@property(nonatomic, copy) NSString *name;

- (void)setZigfoos:(NSArray<Zigfoo *> *)zigfoos {
  // Ensure that we're holding an immutable collection.
  _zigfoos = [zigfoos copy];
}
```

### 使用轻量级泛型来记录包含的类型
所有在Xcode 7或更新版本上编译的项目都应该使用Objective-C轻量级泛型符号来键入包含的对象。

每个NSArray、NSDictionary或NSSet引用都应该使用轻量级泛型声明，以提高类型安全性并显式地记录使用情况。
```obj-c
// GOOD:

@property(nonatomic, copy) NSArray<Location *> *locations;
@property(nonatomic, copy, readonly) NSSet<NSString *> *identifiers;

NSMutableArray<MyLocation *> *mutableLocations = [otherObject.locations mutableCopy];
```

如果完全注释的类型变得复杂，考虑使用typedef来保持可读性。
```obj-c
// GOOD:

typedef NSSet<NSDictionary<NSString *, NSDate *> *> TimeZoneMappingSet;
TimeZoneMappingSet *timeZoneMappings = [TimeZoneMappingSet setWithObjects:...];
```

使用最具描述性的通用超类或协议。在最常见的情况下，如果不知道其他信息，则使用id将集合声明为显式的异构。
```obj-c
// GOOD:

@property(nonatomic, copy) NSArray<id> *unknowns;
```

### 避免抛出异常
不要抛出Objective-C异常，但是您应该准备好从第三方或OS调用中捕获它们。

这遵循了在Apple介绍Cocoa的异常编程主题时使用错误对象进行错误传递的建议。

我们确实使用`-fobjc-exceptions`进行编译(主要是获得`@synchronized`)，但我们不使用`@throw`。当需要正确使用第三方代码或库时，允许使用`@try`、`@catch`和`@finally`。如果您确实使用了它们，请详细记录您希望抛出的方法。

### 检查nil
仅对逻辑流使用nil检查。

使用nil指针检查应用程序的逻辑流，而不是在发送消息时防止崩溃。向nil发送消息可靠地将nil返回为指针，将zero返回为整数或浮点值，将结构初始化为0，将_Complex值返回为{0,0}。

注意，这适用于nil，作为消息目标，而不是参数值。个别方法可能安全处理nil参数值，也可能不安全。

还要注意，这与检查C/ c++指针和块指针的`NULL`不同，`NULL`是运行时无法处理的，会导致应用程序崩溃。您仍然需要确保不解除对空指针的引用。

### BOOL陷阱
将一般的积分值转换为BOOL时要小心。避免直接与“YES”进行比较。

在OS X和32位iOS版本中，BOOL被定义为带符号的char，所以它可能有YES(1)和NO(0)之外的值。

常见的错误包括转换或转换数组的大小、指针值或按位逻辑操作的结果到BOOL，根据整数值的最后一个字节的值，仍然会导致NO值。在将一般的积分值转换为BOOL时，使用三元运算符返回是或否的值。

您可以安全地交换和转换`BOOL`、`_Bool`和`bool`(参见c++ Std 4.7.4、4.12和C99 Std 6.3.1.2)。在Objective-C方法签名中使用`BOOL`。

与BOOL一起使用逻辑运算符(&&，||和!)也是有效的，它将返回可以安全地转换为BOOL的值，而不需要三元运算符。

```obj-c
// AVOID:

- (BOOL)isBold {
  return [self fontTraits] & NSFontBoldTrait;  // AVOID.
}
- (BOOL)isValid {
  return [self stringValue];  // AVOID.
}

// GOOD:

- (BOOL)isBold {
  return ([self fontTraits] & NSFontBoldTrait) ? YES : NO;
}
- (BOOL)isValid {
  return [self stringValue] != nil;
}
- (BOOL)isEnabled {
  return [self isValid] && [self isBold];
}
```

另外，不要直接比较BOOL变量和YES。不仅对于那些精通C语言的人来说更难阅读，而且上面的第一点表明返回值可能并不总是您所期望的那样。
```obj-c
// AVOID:

BOOL great = [foo isGreat];
if (great == YES) {  // AVOID.
  // ...be great!
}
// GOOD:

BOOL great = [foo isGreat];
if (great) {         // GOOD.
  // ...be great!
}
```



### 接口没有实例变量
省略接口上不声明任何实例变量的大括号空集。
```obj-c
// GOOD:

@interface MyClass : NSObject
// Does a lot of stuff.
- (void)fooBarBam;
@end
// AVOID:

@interface MyClass : NSObject {
}
// Does a lot of stuff.
- (void)fooBarBam;
@end
```


## Cocoa格式
- - - -
### 代理模式
当这样做会创建一个retain循环时，不应该保留委托、目标对象和块指针。
为了避免引起retain cycle，在明确不再需要向对象发送消息时，应该立即释放委托或目标指针。
如果没有明确的时间不再需要委托或目标指针，则只能弱地保留该指针。
块指针不能弱地保留。为了避免在客户端代码中造成保留周期，块指针应该只用于回调，只有在调用后或不再需要回调时才能显式释放回调。否则，回调应该通过弱委托或目标指针来完成。


## Objective-C++
- - - -
### 风格匹配语言
在objective - c++源文件中，遵循您要实现的函数或方法的语言风格。在混合Cocoa/Objective-C和c++时，为了最小化不同命名风格之间的冲突，遵循正在实现的方法的风格。
对于@implementation块中的代码，使用Objective-C命名规则。对于c++类方法中的代码，使用c++命名规则。
对于类实现之外的objective - c++文件中的代码，在文件中保持一致。
```obj-c
// GOOD:

// file: cross_platform_header.h

class CrossPlatformAPI {
 public:
  ...
  int DoSomethingPlatformSpecific();  // impl on each platform
 private:
  int an_instance_var_;
};

// file: mac_implementation.mm
#include "cross_platform_header.h"

// A typical Objective-C class, using Objective-C naming.
@interface MyDelegate : NSObject {
 @private
  int _instanceVar;
  CrossPlatformAPI* _backEndObject;
}

- (void)respondToSomething:(id)something;

@end

@implementation MyDelegate

- (void)respondToSomething:(id)something {
  // bridge from Cocoa through our C++ backend
  _instanceVar = _backEndObject->DoSomethingPlatformSpecific();
  NSString* tempString = [NSString stringWithFormat:@"%d", _instanceVar];
  NSLog(@"%@", tempString);
}

@end

// The platform-specific implementation of the C++ class, using
// C++ naming.
int CrossPlatformAPI::DoSomethingPlatformSpecific() {
  NSString* temp_string = [NSString stringWithFormat:@"%d", an_instance_var_];
  NSLog(@"%@", temp_string);
  return [temp_string intValue];
}
```

为了与谷歌的c++风格指南保持一致，项目可以选择使用80列长度限制。


## Objective-C风格的异常
- - - -
### 显示风格异常
不希望遵循这些样式建议的代码行需要在行末尾 NOLINT或在前一行末尾 NOLINTNEXTLINE。有时候，Objective-C代码的某些部分必须忽略这些样式建议(例如，代码可能是机器生成的，或者代码构造不可能正确地样式)。
在该行上的 NOLINT注释或前一行上的 NOLINTNEXTLINE可用于向读者表明代码有意忽略了样式指南。此外，这些注释也可以由自动工具(如linters)获取并正确处理代码。注意，和NOLINT*之间只有一个空格。










