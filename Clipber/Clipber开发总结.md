# Clipber开发总结

## * 起因

AppStore上已经有几个类似的软件了。但，有的用户着不顺手，有的很久不更新了。

1. Snippy已经很久不更新了，原来的软件在新的OSX系统上有一些问题；
2. CopyClip和Pin只支持文本；
3. 腾讯出品的截图只是简单的截图工具。

## * 项目搭建

1. UI布局

    选用sb/xib来进行UI的创建。毕竟是个人开发，使用sb/xib还是可以节省很多工作量的。但其中也遇到一些问题。当然，主要原因还是因为不熟悉，毕竟工作中是全代码写UI的，sb/xib是被禁止使用的。

2. 数据库

    候选项：Core Data, Realm, FMDB, YapDataBase

    **Core Data** : 这次没有选择原生的core data，这个之前有做过一些小的demo，但还是不熟悉，很多内容需要结合xcode一起使用。但有些功能的确很偏，出了问题也不好找答案。

    **Realm** : realm之前在做一个扫码app的时候用到的。使用起来是很方便，文档什么的也不错。但有个最大的问题：包过大，使用realm后所带来的包的大小的增幅完全不可接受。有兴趣大家可以自己试下。

    **YapDataBase** : 之前在做IM的时候有接触过这个。但在功能上还是过度封装了。不太适合这次的简单需求。

    **FMDB** : 这次直接选择FMDB了，FMDB非常灵活，大概是因为没有做过多的封装吧，提供的功能很基础。这次app在功能上也不需要太复杂的数据存储。

3. cocoapods

    呃，语言使用OC，而且之前对Carthage也没有什么认知，这次也是为了快速出产品，所以直接选用熟悉cocoapods了。集成了几个第三方库：（1）FMDB；（2）libextobjc；（3）GVUserDefaults。当然还对几个库进行了修改，原来的库有bug或者不支持OSX。

## * 功能实现

Clipber的功能还是很简单的，没有集成啥无用的功能（目前为止2019-05-12）

1. 剪切板监听

    使用定时器对系统剪切板进行轮询，以实现监听。其实一直想找一种通知的方式，当剪切板发生变化的时候，直接通知到Clipber。但，找了一圈，无论是Google、Github、stackoverflow都没有相应的解决方案。尝试使用KVO也不成功。所以最后只能通过轮询来实现了。如果你知道相应的解决方案，欢迎提点一下。

2. 截图
3. 快捷键
4. 开机自启动

    这个是折腾最久的，倒不是说有多难实现，就是实现后总有这样或者那样的问题，处理起来特别诡异。而且自启动服务无法让用户在系统设置中进行管理。所以，最后也是把这个功能先隐藏掉了。需要自启动的话，可能通过系统设置，自行添加。

## * 内存泄漏

对内存进行检查是一个好的习惯。毕竟内存泄漏这事，大概率不会影响功能使用。但还是需要对自己做出的产品负责的。使用xcode自己的instrument对内存泄漏进行了检查，发现截图窗口在捕获的时候有内存没有释放。内存泄漏最难的不是发现有内存泄漏，而是定位。我目前都是通过xcode内存检查来看引用计数。虽然分析起来有点麻烦，但还是行之有效的。（如果你还有别的好的方案，欢迎提点一下）。定位到的问题点，解决一下就好了。

## * 内存优化

第一版的截图没有对图片进行压缩，所以app的内存占用还是有点不那么好看的。其实截图的原文件是放在本地的。数据库里只保存了一个路径。所以，Clipber显示的时候只需要加载一个压缩的图片就好。但是通过以下方法无法得到一个好的压缩效果。而且递归调用也无法继续压缩。
```objective-c
NSDictionary *imageProps = [NSDictionary dictionaryWithObject:[NSNumber numberWithFloat:aimRate] forKey:NSImageCompressionFactor];
NSData *data = [imageRep representationUsingType:NSBitmapImageFileTypeJPEG properties:imageProps];
```
所以在生成`NSBitmapImageRep`时修改了一下。
```
NSBitmapImageRep *rep = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes: NULL 
                                                                    pixelsWide: width
                                                                    pixelsHigh: height
                                                                 bitsPerSample: 8
                                                               samplesPerPixel: 4
                                                                      hasAlpha: YES
                                                                      isPlanar: NO
                                                                colorSpaceName: NSDeviceRGBColorSpace
                                                                   bytesPerRow: width * 4
                                                                  bitsPerPixel: 32];
```
通过这种方式来直接对数据源进行有损压缩，以生成对应的缩略图。

## * 多语言

因为这次使用了sb/xib，所以多语言分两块：（1）代码；（2）sb/xib。
代码中的好说，项目语言配置百度一下就好了，这个很简单。然后，使用系统宏读取一下语言文件就好了。只是sb/xib就点伤了。不是伤在初始设置上（初始化还是很简单的），而是在对xib进行新加内容或者修改的时候。例如：之前已经生成过一次xib的多语言，然后再添加新的内容时，新加的内容并没有自动同步到多语言配置中。还得自己弄，有点坑。毕竟xib已经是一个被界面化的配置文件。设置多语言的时候又回到文本配置界面上了。尴尬。（不排除有我不知道的方法。你知道我想说啥：如果你还有别的好的方案，欢迎提点一下）

## * 上架

就被拒绝了一次，比我表白被拒绝的次数还要少。

## * 问题总结

1. SB、xib
