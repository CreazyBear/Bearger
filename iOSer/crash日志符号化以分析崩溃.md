# crash日志符号化，以分析崩溃
#程序员/iOS/项目设置



#### 为什么需要符号化

```
Filtered syslog:
None found

Thread 0 name:  Dispatch queue: com.apple.main-thread
Thread 0:
0   ...gou.sogouinput.BaseKeyboard	0x0000000100196000 0x100030000 + 1466368
1   ...gou.sogouinput.BaseKeyboard	0x0000000100191ca4 0x100030000 + 1449124
2   ...gou.sogouinput.BaseKeyboard	0x00000001001aadd8 0x100030000 + 1551832
3   ...gou.sogouinput.BaseKeyboard	0x00000001003942b0 0x100030000 + 3556016
4   ...gou.sogouinput.BaseKeyboard	0x0000000100393b30 0x100030000 + 3554096
5   ...gou.sogouinput.BaseKeyboard	0x00000001003c7bbc 0x100030000 + 3767228
6   ...gou.sogouinput.BaseKeyboard	0x00000001003c8a48 0x100030000 + 3770952
7   ...gou.sogouinput.BaseKeyboard	0x0000000100103158 0x100030000 + 864600
8   ...gou.sogouinput.BaseKeyboard	0x0000000100391d10 0x100030000 + 3546384
9   ...gou.sogouinput.BaseKeyboard	0x00000001003912c4 0x100030000 + 3543748
10  ...gou.sogouinput.BaseKeyboard	0x00000001003b4ba4 0x100030000 + 3689380
11  ...gou.sogouinput.BaseKeyboard	0x0000000100268174 0x100030000 + 2326900
12  ...gou.sogouinput.BaseKeyboard	0x0000000100268580 0x100030000 + 2327936

```

上面是sogou输入法的crash日志。像`0x100030000 + 1466368`这样的都只给出了内存的崩溃地址。但无法得到是哪个方法调用。符号化就是将这些内存地址转换成对应的可读性方法。



#### 准备相关文件

要对crash日志进行符号化，需要四样东西：

1. 从设备上导出的crash 日志
2. `.dSYM`文件
3. `.app`文件
4. `symbolicatecrash`工具

##### crash 日志的导出

将设备连接电脑，`xcode->Window->devices->选择设备->view device log->找到对应的crash日志->export log`。



##### 生成`.dSYM`文件

crash日志的符号化主要借助dSYM文件。得到dSYM文件的方法：`Target -> Build Setting -> Debug Information Format -> DWARF with dSYM File`。这样就可以和.app一起生成.dSYM文件了。



##### app`文件

略



##### symbolicatecrash工具

1. 找到symbolicatecrash

```sh
find /Applications/Xcode.app -name symbolicatecrash -type f11
```

2. 稍等一会就会有路径输出，这个路径就是symbolicatecrash的路径

   路径可能不止一个，随意选一个

```sh
/Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash
```

3. 用命令将symbolicatecrash拷贝到桌面的crash文件夹里面，与.app和.app.dSYM放一起（手动找到symbolicatecrash，拷贝出来也行）

   当然，不cp也可以的。为了清楚和方便使用，copy出来放一起。

```sh
cp /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash ~/Desktop
export DEVELOPER_DIR="/Applications/XCode.app/Contents/Developer"
```



#### 符号化

```sh
./symbolicatecrash XXXApp.crash XXXApp.app > crash.log
```



#### 结果

对比.crash和.log文件可以发现地址被替换成方法名、代码行等有用信息。

```
//部分内容被修改过了
Filtered syslog:
None found

Last Exception Backtrace:
0   CoreFoundation                	0x1894fafe0 __exceptionPreprocess + 124
1   libobjc.A.dylib               	0x187f5c538 objc_exception_throw + 56
2   CoreFoundation                	0x1894faca8 -[NSException raise] + 12
3   Foundation                    	0x189f1068c -[NSObject(NSKeyValueCoding) setValue:forKey:] + 272
4   XXXXApp                      	0x102fb5850 -[XXXXAppManager enterRooms] (XXXXAppManager.m:33)
5   XXXXApp                      	0x102f6de2c +[XXXXAppSDK showChatModule] (XXXXAppSDK.m:23)
6   XXXXApp                      	0x100a918d4 +[XXXXAppManager callName:selectorName:params:] (XXXXAppManager.m:28)
7   XXXXApp                      	0x100278bfc -[XXXXAppTopView buttonClick:] (XXXXAppView.m:112)
8   UIKit                         	0x18f661c54 -[UIApplication sendAction:to:from:forEvent:] + 96
```

```
Filtered syslog:
None found

Last Exception Backtrace:
0   CoreFoundation                	0x1894fafe0 __exceptionPreprocess + 124
1   libobjc.A.dylib               	0x187f5c538 objc_exception_throw + 56
2   CoreFoundation                	0x1894faca8 -[NSException raise] + 12
3   Foundation                    	0x189f1068c -[NSObject(NSKeyValueCoding) setValue:forKey:] + 272
4   XXXXApp                      	0x102fb5850 0x1000d4000 + 49158224
5   XXXXApp                      	0x102f6de2c 0x1000d4000 + 48864812
6   XXXXApp                      	0x100a918d4 0x1000d4000 + 10213588
7   XXXXApp                      	0x100278bfc 0x1000d4000 + 1723388
8   UIKit                         	0x18f661c54 -[UIApplication sendAction:to:from:forEvent:] + 96

```



* debug 模式下跑的包，产生的crash日志不需要符号化
* 线上环境app集成了Crashlytics，上报的crash已经符号化了
* 主要是回归测试阶段对于部分崩溃需要这种分析方式