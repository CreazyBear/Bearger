# CodeSnippet
#程序员/ios/代码段

### 内存警告通知
```objective-c
#if TARGET_OS_IPHONE
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(handleMemoryWarning) name:UIApplicationDidReceiveMemoryWarningNotification object:nil];
#endif
```

### QRCode Generate
```objective-c
- (CIImage *)createQRForString:(NSString *)qrString
{
    // Need to convert the string to a UTF-8 encoded NSData object
    NSData *stringData = [qrString dataUsingEncoding:NSUTF8StringEncoding];

    // Create the filter
    CIFilter *qrFilter = [CIFilter filterWithName:@"CIQRCodeGenerator"];
    // Set the message content and error-correction level
    [qrFilter setValue:stringData forKey:@"inputMessage"];
    [qrFilter setValue:@"H" forKey:@"inputCorrectionLevel"];

    // Send the image back
    return qrFilter.outputImage;
}

- (UIImage *)createNonInterpolatedUIImageFromCIImage:(CIImage *)image withScale:(CGFloat)scale
{
    // Render the CIImage into a CGImage
    CGImageRef cgImage = [[CIContext contextWithOptions:nil] createCGImage:image fromRect:image.extent];

    // Now we'll rescale using CoreGraphics
    UIGraphicsBeginImageContext(CGSizeMake(image.extent.size.width * scale, image.extent.size.width * scale));
    CGContextRef context = UIGraphicsGetCurrentContext();
    // We don't want to interpolate (since we've got a pixel-correct image)
    CGContextSetInterpolationQuality(context, kCGInterpolationNone);
    CGContextDrawImage(context, CGContextGetClipBoundingBox(context), cgImage);
    // Get the image out
    UIImage *scaledImage = UIGraphicsGetImageFromCurrentImageContext();
    // Tidy up
    UIGraphicsEndImageContext();
    CGImageRelease(cgImage);
    return scaledImage;
}

- (IBAction)handleGenerateButtonPressed:(id)sender {
    // Disable the UI
    [self setUIElementsAsEnabled:NO];
    [self.stringTextField resignFirstResponder];

    // Get the string
    NSString *stringToEncode = self.stringTextField.text;

    // Generate the image
    CIImage *qrCode = [self createQRForString:stringToEncode];

    // Convert to an UIImage
    UIImage *qrCodeImg = [self createNonInterpolatedUIImageFromCIImage:qrCode withScale:2*[[UIScreen mainScreen] scale]];

    // And push the image on to the screen
    self.qrImageView.image = qrCodeImg;

    // Re-enable the UI
    [self setUIElementsAsEnabled:YES];
}
```


### 文字转图片
```swift
    func createImageForMessage() -> UIImage? {
        let background = UIView(frame: CGRect(x: 0, y: 0, width: 300, height: 300))
        background.backgroundColor = UIColor.white
        
        let label = UILabel(frame: CGRect(x: 75, y: 75, width: 150, height: 150))
        label.font = UIFont.systemFont(ofSize: 56.0)
        label.backgroundColor = UIColor.red
        label.textColor = UIColor.white
        label.text = "1"
        label.textAlignment = .center
        label.layer.cornerRadius = label.frame.size.width/2.0
        label.clipsToBounds = true
        
        background.addSubview(label)
        background.frame.origin = CGPoint(x: view.frame.size.width, y: view.frame.size.height)
        view.addSubview(background)
        
        UIGraphicsBeginImageContextWithOptions(background.frame.size, false, UIScreen.main.scale)
        background.drawHierarchy(in: background.bounds, afterScreenUpdates: true)
        let image = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()
        
        background.removeFromSuperview()
        
        return image
    }

```


### 获取文字宽度

```objective-c
[content tn_sizeWithFont:[UIFont systemFontOfSize:fontSize] constrainedToSize:CGSizeMake(MAXFLOAT, 30)]
```

### 字符集

```objective-c
static NSString * const kWSCharactersGeneralDelimitersToEncode = @":#[]@"; // does not include "?" or "/" due to RFC 3986 - Section 3.4
static NSString * const kWSCharactersSubDelimitersToEncode = @"!$&'()*+,;=";

NSMutableCharacterSet * allowedCharacterSet = [[NSCharacterSet URLQueryAllowedCharacterSet] mutableCopy];
[allowedCharacterSet removeCharactersInString:[kWSCharactersGeneralDelimitersToEncode stringByAppendingString:kWSCharactersSubDelimitersToEncode]];

```
    
### 初始化TableView

```objective-c
- (void)setupTableView
{
    self.tableView = [[UITableView alloc] initWithFrame:CGRectMake(0.0f, -10.0f, SCREEN_WIDTH,SCREEN_HEIGHT+10) style:UITableViewStylePlain];
    self.tableView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
    self.tableView.backgroundColor = HEXCOLOR(0xf2f3f8);
    self.tableView.dataSource = self;
    self.tableView.delegate = self;
    self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
    self.tableView.scrollsToTop = YES;
    [self.tableView registerClass:[TNChatCSCOrderListCell class] forCellReuseIdentifier:customerServiceCenterOrderListCellIdentifier];
    [self.view addSubview:self.tableView];
}
```

###  点击事件

```objective-c
-(void)handleTabGesture:(UITapGestureRecognizer *)tap
{
    CGPoint touchPt = [tap locationInView:self];
    
    if (CGRectContainsPoint(self.avatarImageView.frame, touchPt))
    {
        if (self.delegate && [self.delegate respondsToSelector:@selector(messageTableViewCell:showMemberInfo:)])
        {
            [self.delegate messageTableViewCell:self showMemberInfo:self.messageViewModelEntity.messageEntity];
        }
    }
    else if (CGRectContainsPoint(self.failedMarkImageView.frame, touchPt))
    {
        if (!self.failedMarkImageView.hidden)
        {
            
            [self handleTapFailedMarkImageView:tap];
        }
    }
}
```

### 裁剪图片
```objective-c
+ (UIImage *)cutImage:(UIImage *)image fraction:(CGFloat)fractonPart{

    CGFloat width = image.size.width * fractonPart * image.scale;
    CGRect newFrame = CGRectMake(0, 0, width , image.size.height * image.scale);
    CGImageRef resultImage = CGImageCreateWithImageInRect(image.CGImage, newFrame);
    UIImage *result = [UIImage imageWithCGImage:resultImage scale:image.scale orientation:image.imageOrientation];
    CGImageRelease(resultImage);

    return result;
}
```


### runtime
```objective-c
#import "TFRuntimeManager.h"

#import <objc/message.h>

@implementation TFRuntimeManager
const char *kPropertyListKey = "YFPropertyListKey";
static Class archiveModelClass;
static id archiveModel;
#pragma mark -- 获得方法、属性
+ (NSArray *)TF_getAllMethodWithClass:(Class)className {
    NSMutableArray *methodNames = [NSMutableArray array];
    unsigned int count = 0;
    //获取所有方法
    Method *methodList = class_copyMethodList(className, &count);
    for (int i = 0; i < count; i++) {
        SEL  method = method_getName(methodList[i]);
        NSString *methodName = [NSString stringWithCString:sel_getName(method) encoding:NSUTF8StringEncoding];
        [methodNames addObject:methodName];
    }
    return methodNames.copy;
}
+ (NSArray *)TF_getAllIvarWithClass:(Class )className {
    NSMutableArray *ivarNames = [NSMutableArray array];
    unsigned int count = 0;
    //获取所有方法
    Ivar *ivarList = class_copyIvarList(className, &count);
    for (int i = 0; i < count; i++) {
        Ivar var = ivarList[i];
        NSString *ivarName = [NSString stringWithCString:ivar_getName(var) encoding:NSUTF8StringEncoding];
        [ivarNames addObject:ivarName];
    }
    return ivarNames.copy;
    
}
#pragma mark -- 添加方法
+ (BOOL)TF_addMethodWithClass:(Class )className methodName:(NSString *)methodName IMPClass:(Class)impClassName impName:(NSString *)impName type:(NSString *)type {
    const char *methodType = [type UTF8String];
    BOOL isSuccess = class_addMethod(className, NSSelectorFromString(methodName), class_getMethodImplementation(impClassName, NSSelectorFromString(impName)), methodType);
    return isSuccess;
}
+ (BOOL)TF_addMethodWithWithClass:(Class )className
                        methodSel:(SEL)methodSel
              implementationClass:(Class)implementationClass
                implementationSel:(SEL)implementationSel {
    
    BOOL isSuccess = class_addMethod(className, methodSel, class_getMethodImplementation(implementationClass, implementationSel), method_getTypeEncoding(class_getInstanceMethod(className, methodSel)));
    return isSuccess;
}



#pragma mark -- 交换两个方法
+ (void)TF_exchangeMethodSourceClass:(Class)sourceClass
                           sourceSel:(SEL)sourceSel
                         targetClass:(Class)targetClass
                           targetSel:(SEL)targetSel{
    Method sourceM = class_getInstanceMethod(sourceClass, sourceSel);
    Method targetM  = class_getInstanceMethod(targetClass, targetSel);
    method_exchangeImplementations(sourceM, targetM);
}
#pragma mark -- 取代方法
+ (void)TF_replaceMethodSourceClass:(Class)sourceClass
                          sourceSel:(SEL)sourceSel
                        targetClass:(Class)targetClass
                          targetSel:(SEL)targetSel {
    Method targetM  = class_getInstanceMethod(targetClass, targetSel);
    class_replaceMethod(sourceClass, sourceSel, method_getImplementation(targetM), method_getTypeEncoding(targetM));
}
#pragma mark -- 字典模型转换
+ (Class)TF_modelWithDict:(NSDictionary *)dict
                 model:(Class)model {
    id objc = [[model alloc] init];
    
    NSArray *propertryList = [self TF_getAllObjcProperties:model];
    [dict enumerateKeysAndObjectsUsingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
        if ([propertryList containsObject:key]) {
            [objc setValue:obj forKey:key];
        }
        
    }];
    return objc;
}
+ (NSArray *)TF_getAllObjcProperties:(Class)model {
    //获取关联对象
    NSArray *ptyList = objc_getAssociatedObject(model, kPropertyListKey);
    if (ptyList) {//如果有值直接返回
        return ptyList;
    }
    unsigned int outCount = 0;
    objc_property_t *propertyList = class_copyPropertyList(model, &outCount);
    NSMutableArray *mtArray = [NSMutableArray array];
    for (int i = 0; i < outCount ; i ++) {
        
        objc_property_t property = propertyList[i];
        NSString *propertyName = [NSString stringWithCString: property_getName(property) encoding:NSUTF8StringEncoding];
        [mtArray addObject:propertyName];
    }
    //设置关联对象
    objc_setAssociatedObject(model, kPropertyListKey, mtArray.copy, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    free(propertyList);
    return mtArray;
}
#pragma mark -- 归档解档
+ (BOOL)TF_archive:(Class)modelClass
             model:(id)model
          filePath:(NSString *)path {
   [[self alloc] TF_archiveAndUnarchive:modelClass model:model];  
    BOOL result = [NSKeyedArchiver archiveRootObject:model toFile:path];
    if (result) {
        return YES;
    }
    return NO;
}
+ (id)TF_unarchive:(Class)modelClass
          filePath:(NSString *)path {
       [[self alloc] TF_archiveAndUnarchive:modelClass model:nil];
  return [NSKeyedUnarchiver unarchiveObjectWithFile:path];
}
- (void)TF_archiveAndUnarchive:(Class)modelClass model:(id)model {
   archiveModelClass = modelClass;
    archiveModel = model;
    [TFRuntimeManager TF_replaceMethodSourceClass:modelClass sourceSel:@selector(initWithCoder:) targetClass:[TFRuntimeManager class] targetSel:@selector(initWithCoder:)];
    [TFRuntimeManager TF_replaceMethodSourceClass:modelClass sourceSel:@selector(encodeWithCoder:) targetClass:[TFRuntimeManager class] targetSel:@selector(encodeWithCoder:)];
}
- (instancetype)initWithCoder:(NSCoder *)coder
{
    
    archiveModel = [super init];
    if (archiveModel) {
        NSArray *ivarNames = [TFRuntimeManager TF_getAllIvarWithClass:archiveModelClass];
        for (NSString * key  in ivarNames) {
            id value = [coder decodeObjectForKey:key];
            [archiveModel setValue:value forKey:key];
        }
    }
    return archiveModel;
}

- (void)encodeWithCoder:(NSCoder *)coder{
   NSArray *ivarNames = [TFRuntimeManager TF_getAllIvarWithClass:archiveModelClass];
    for (NSString *key in ivarNames) {
        id value = [archiveModel valueForKey:key];
        [coder encodeObject:value forKey:key];
    }
    
}


@end

```


### 崩溃分析
```
    由于923发包时候，没有把上传到Fabric的开关打开，导致923不会上传DSYMs的文件到Fabric上面，以下的统计是参考elk和每万个session崩溃数估
```


### 自动布局

```objective-c
    //NSLayoutAnchor    
    
    rootNodeView.translatesAutoresizingMaskIntoConstraints = NO;

    [rootNodeView.leftAnchor constraintEqualToAnchor:parentView.leadingAnchor constant:20].active = YES;

```

### 取消第一响应
```objective-c
//UIKit-->UIGestureRecognizer-->touchesBegan:withEvent:
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    [self.textView resignFirstResponder];
}
```


### 主线程等待但不阻塞
```objective-c
        while ([param valueForKey:@"waitForDebuging"]) {
            [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
        }
```


### 调试指令
```lldb
image lookup -r -s webThread
```


### ios thread info
```objective-c
-(void)getWebThread
{
    float cpu_usage;
    
    kern_return_t           kr;
    thread_array_t          thread_list;
    mach_msg_type_number_t  thread_count;
    mach_msg_type_number_t  thread_info_count;
    
    thread_info_data_t      thinfo;
    thread_basic_info_t     basic_info_th;
    thread_extended_info_t  extended_info_th;
    
    kr = task_threads(mach_task_self(), &thread_list, &thread_count);
    if (kr != KERN_SUCCESS) {
        self.webThread = nil;
    }
    cpu_usage = 0;
    
    for (int i = 0; i < thread_count; i++)
    {
//        thread_info_count = THREAD_INFO_MAX;
        thread_info_count = THREAD_EXTENDED_INFO_COUNT;
        
        
        kr = thread_info(thread_list[i], THREAD_EXTENDED_INFO, (thread_info_t)thinfo, &thread_info_count);
        if (kr != KERN_SUCCESS) {
            self.webThread = nil;
        }
        
//        basic_info_th = (thread_basic_info_t)thinfo;
        extended_info_th = (thread_extended_info_t)thinfo;
        TNLogInfo(@"%s",extended_info_th->pth_name);
    }
}

```

### 类的唯一标识
```objective-c
self.cache.name = [[NSString alloc] initWithFormat:@"%@.%@.%p", NSStringFromClass([self class]), self.name, self];


+(NSString *)uuidString
{
    CFUUIDRef uuid_ref = CFUUIDCreate(NULL);
    CFStringRef uuid_string_ref= CFUUIDCreateString(NULL, uuid_ref);
    NSString *uuid = [NSString stringWithString:(__bridge NSString *)uuid_string_ref];
    CFRelease(uuid_ref);
    CFRelease(uuid_string_ref);
    return [uuid lowercaseString];
}
```

### weak
```objective-c

    __weak __typeof(self) weakSelf = self;
    
    dispatch_async(self.queue, ^{
        __typeof(weakSelf) strongSelf = weakSelf;
        if (!strongSelf)
            return;
        //code
    });

```


### 内存检查
[xcode - Determining the available amount of RAM on an iOS device - Stack Overflow](https://stackoverflow.com/questions/5012886/determining-the-available-amount-of-ram-on-an-ios-device)

```Objective-C
#import <mach/mach.h>
#import <mach/mach_host.h>

void print_free_memory ()
{
    mach_port_t host_port;
    mach_msg_type_number_t host_size;
    vm_size_t pagesize;

    host_port = mach_host_self();
    host_size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    host_page_size(host_port, &pagesize);        

    vm_statistics_data_t vm_stat;

    if (host_statistics(host_port, HOST_VM_INFO, (host_info_t)&vm_stat, &host_size) != KERN_SUCCESS) {
        NSLog(@"Failed to fetch vm statistics");
    }

    /* Stats in bytes */ 
    natural_t mem_used = (vm_stat.active_count +
                          vm_stat.inactive_count +
                          vm_stat.wire_count) * pagesize;
    natural_t mem_free = vm_stat.free_count * pagesize;
    natural_t mem_total = mem_used + mem_free;
    NSLog(@"used: %u free: %u total: %u", mem_used, mem_free, mem_total);
}
```

### 摇一摇


打开 ViewController.swift 文件，首先要让 View Controller 回应点击事件，可以通过 ViewController FirstResponder 实现，添加下列方法：

```
override func becomeFirstResponder() -> Bool {
    return true
}
```

接下来，要想检测摇一摇手势，添加 motionEnded(_:with:) 方法。

```
override func motionEnded(_ motion: UIEventSubtype, with event: UIEvent?) {
    if motion == .motionShake {
        shakeLabel.text = "Shaken, not stirred"
    }
}
```

### 可变参数数组

有点类似C++里面的Iterator。
> 需要注意的是，调用的时候最后一个需要是nil。
> （越看越像Iterator）

```
-(void)start{
    [self multiArgument:@"one",@"two",@"two",@"two",nil];
}

-(void)multiArgument:(NSString*)arg,...
{
    NSMutableArray * argArray = [NSMutableArray new];
    va_list argList;
    va_start(argList, arg);
    for (NSString *ele = arg; ele != nil; ele = va_arg(argList, NSString *)) {
        [argArray addObject:ele];
    }
    va_end(argList);
    NSLog(@"%@",argArray);
}
```

### COMMAND + R 监听


```Objective-C
#pragma mark 模拟器键盘事件
- (NSArray *)keyCommands
{
    
    static NSArray *keyCommands;
    
#if TARGET_IPHONE_SIMULATOR
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        UIKeyCommand *composeCommand = [UIKeyCommand keyCommandWithInput:@"r"
                                                           modifierFlags:UIKeyModifierCommand
                                                                  action:@selector(composeShortcut:)];
        keyCommands = @[composeCommand];
    });
#endif
    return keyCommands;
}


- (void)composeShortcut:(UIKeyCommand *)command
{
    [self reloadAction];
}

```