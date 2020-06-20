# NSRunLoopMode
#程序员/iOS/线程


1. NSRunLoopCommonModes
Objects added to a run loop using this value as the mode are monitored by all run loop modes that have been declared as a member of the set of “common" modes; see the description of CFRunLoopAddCommonMode for details.

Availability
iOS 2.0+
macOS 10.5+
tvOS 9.0+
watchOS 2.0+

2. NSDefaultRunLoopMode
The mode to deal with input sources other than NSConnection objects.

Discussion
This is the most commonly used run-loop mode.

Availability
iOS 2.0+
macOS 10.0+
tvOS 9.0+
watchOS 2.0+

3. NSEventTrackingRunLoopMode
A run loop should be set to this mode when tracking events modally, such as a mouse-dragging loop.

Availability
macOS 10.0+

4. NSModalPanelRunLoopMode
A run loop should be set to this mode when waiting for input from a modal panel, such as NSSavePanel or NSOpenPanel.

Availability
macOS 10.0+

5. UITrackingRunLoopMode
The mode set while tracking in controls takes place. You can use this mode to add timers that fire during tracking.

Availability
iOS 2.0+
tvOS 9.0+

一共五种，去掉Mac OS的，剩下三种：
NSRunLoopCommonModes
NSDefaultRunLoopMode
UITrackingRunLoopMode
那么这三种的差异在哪呢？

下面这篇文章写得很清楚
[深入理解RunLoop - CocoaChina_让移动开发更简单](http://www.cocoachina.com/ios/20150601/11970.html)
 **从中摘出对应的内容如下：**
> 有个概念叫 "CommonModes"：一个 Mode 可以将自己标记为"Common"属性（通过将其 ModeName 添加到 RunLoop 的 "commonModes" 中）。每当 RunLoop 的内容发生变化时，RunLoop 都会自动将 _commonModeItems 里的 Source_Observer_Timer 同步到具有 "Common" 标记的所有Mode里。  
>   
> 应用场景举例：主线程的 RunLoop 里有两个预置的 Mode：kCFRunLoopDefaultMode 和 UITrackingRunLoopMode。这两个 Mode 都已经被标记为"Common"属性。DefaultMode 是 App 平时所处的状态，TrackingRunLoopMode 是追踪 ScrollView 滑动时的状态。当你创建一个 Timer 并加到 DefaultMode 时，Timer 会得到重复回调，但此时滑动一个TableView时，RunLoop 会将 mode 切换为 TrackingRunLoopMode，这时 Timer 就不会被回调，并且也不会影响到滑动操作。  
>   
> 有时你需要一个 Timer，在两个 Mode 中都能得到回调，一种办法就是将这个 Timer 分别加入这两个 Mode。还有一种方式，就是将 Timer 加入到顶层的 RunLoop 的 "commonModeItems" 中。"commonModeItems" 被 RunLoop 自动更新到所有具有"Common"属性的 Mode 里去。  



eg:在SDWebImageView的2.0之前的版本中，就存在这样一个问题：
```
Replace the NSOperation based downloader by a simple async NSURLConnection (read-on to understand why)

I finally found the reason behind the download not started while UITableView is manipulated: the default NSURLConnection runloop mode. Its default mode is NSEventTrackingRunLoopMode which is interrupted by UI events. Changing default NSURLConnection runloop mode to NSRunLoopCommonModes just fix this good old responsiveness issue.
```
大体意思就是说`NSOperation `中的下载任务在`UITableView`滑动时，无法开始。主要是NSDefaultRunLoopMode为默认mod引起的。

后来又对此处做了其它修改并添加注释：
>  If not in low priority mode, ensure we aren't blocked by UI manipulations (default runloop mode for NSURLConnection is NSEventTrackingRunLoopMode)  

注释中强调 `NSURLConnection `的默认mod是`NSEventTrackingRunLoopMode` =。=