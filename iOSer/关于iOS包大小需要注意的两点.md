# 关于iOS包大小需要注意的两点
#程序员/iOS/项目设置

#### 代码段大小限制

![代码段大小限制](http://upload-images.jianshu.io/upload_images/2043160-e5e529a356843378.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](%E5%85%B3%E4%BA%8EiOS%E5%8C%85%E5%A4%A7%E5%B0%8F%E9%9C%80%E8%A6%81%E6%B3%A8%E6%84%8F%E7%9A%84%E4%B8%A4%E7%82%B9/2043160-e5e529a356843378.png.jpeg)


官方文档出处：
https://developer.apple.com/library/content/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/SubmittingTheApp.html#//apple_ref/doc/uid/TP40011225-CH33-SW1

#### 包大小
包的大小其实没有限制，不过iOS对于超过100M的包禁止使用移动网络下载。也就是说当用户开启了自动更新时，移动网络也不会自动更新应用。这就一定程度上影响新功能的推广。




- - - -
其它参考文章：

[Technical Q&A QA1795: Reducing the size of my App](https://developer.apple.com/library/content/qa/qa1795/_index.html)

[Technical Q&A QA1779: Reducing Download Size for iOS App Updates](https://developer.apple.com/library/content/qa/qa1779/_index.html)

[On-Demand Resources Guide: Platform Sizes for On-Demand Resources](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/PlatformSizesforOn-DemandResources.html#//apple_ref/doc/uid/TP40015083-CH23-SW1)

[On-Demand Resources Guide中文版（按需加载资源--上） - CocoaChina_让移动开发更简单](http://www.cocoachina.com/ios/20150615/12155.html)

[On-Demand Resources Guide中文版（按需加载资源--下） - CocoaChina_让移动开发更简单](http://www.cocoachina.com/ios/20150615/12152.html)