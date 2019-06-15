# source

> https://guides.cocoapods.org/syntax/podfile.html#source

之前在介绍`.cocoapods`这个目录的时候就讲到过。这个库类似一个目录，用于帮助cocoapod找到对应的库。所以，参照着官方库创建一个对应的git仓库就好了。

我只是很好奇，有没有办法自动生成一个对应的私有库？
下面是一个自建私有cocoapod库官方的库。

```rb
source 'https://github.com/artsy/Specs.git'
source 'https://github.com/CocoaPods/Specs.git'
```
