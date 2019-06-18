# Cocoa Pods 创建私有仓库
#程序员/iOS/项目设置

需要注意的细节太多，这里只记录一下大致的流程。

#### 首先在本地创建一个cocoa pod库。
```
cd [To the dir you want to git create a template]
pod lib create [Your Cocoapod Spec Name]
```


如果执行上面的命令报错：
```sh
cannot load such file -- colored2 (LoadError)
```
那么
```sh
sudo gem install colored2
```


输入以上指令后，就根据提示设置一下就好了：
```
➜  TestGitCreate git:(master) pod lib create TEST
Cloning `https://github.com/CocoaPods/pod-template.git` into `TEST`.
Configuring TEST template.

------------------------------

To get you started we need to ask a few questions, this should only take a minute.

If this is your first time we recommend running through with the guide: 
 - http://guides.cocoapods.org/making/using-pod-lib-create.html
 ( hold cmd and double click links to open in a browser. )


What language do you want to use?? [ Swift / ObjC ]
 > ObjC

Would you like to include a demo application with your library? [ Yes / No ]
 > No

Which testing frameworks will you use? [ Specta / Kiwi / None ]
 > None

Would you like to do view based testing? [ Yes / No ]
 > No

What is your class prefix?
 > Bear

Running pod install on your new library.

Analyzing dependencies
Fetching podspec for `TEST` from `../`
Downloading dependencies
Installing TEST (0.1.0)
Generating Pods project
Integrating client project

[!] Please close any current Xcode sessions and use `TEST.xcworkspace` for this project from now on.
Sending stats
Pod installation complete! There is 1 dependency from the Podfile and 1 total pod installed.

 Ace! you're ready to go!
 We will start you off by opening your project in Xcode
  open 'TEST/Example/TEST.xcworkspace'

To learn more about the template see `https://github.com/CocoaPods/pod-template.git`.
To learn more about creating a new pod, see `http://guides.cocoapods.org/making/making-a-cocoapod`.
```

#### 设置`.podspec`文件
照着注释改就好了，不行就看下官方文档了

```
#
# Be sure to run `pod lib lint TEST.podspec' to ensure this is a
# valid spec before submitting.
#
# Any lines starting with a # are optional, but their use is encouraged
# To learn more about a Podspec see http://guides.cocoapods.org/syntax/podspec.html
#

Pod::Spec.new do |s|
  s.name             = 'TEST'
  s.version          = '0.1.0'
  s.summary          = 'A short description of TEST.'

# This description is used to generate tags and improve search results.
#   * Think: What does it do? Why did you write it? What is the focus?
#   * Try to keep it short, snappy and to the point.
#   * Write the description between the DESC delimiters below.
#   * Finally, don't worry about the indent, CocoaPods strips it!

  s.description      = <<-DESC
TODO: Add long description of the pod here.
                       DESC

  s.homepage         = 'https://github.com/WalterCreazyBear/TEST'
  # s.screenshots     = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'
  s.license          = { :type => 'MIT', :file => 'LICENSE' }
  s.author           = { 'WalterCreazyBear' => '597370561@qq.com' }
  s.source           = { :git => 'https://github.com/WalterCreazyBear/TEST.git', :tag => s.version.to_s }
  # s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'

  s.ios.deployment_target = '8.0'

  s.source_files = 'TEST/Classes/**/*'
  
  #不解开注释，需要将项目中对应Asset文件夹干掉！
  #解开注释，如果一个匹配也找不到，也会不通过验证！so~
  # s.resource_bundles = {
  #   'TEST' => ['TEST/Assets/*.png']
  # }

  # 如果需要对外暴露.h文件，这里需要将所有需要暴露的.h文件都加在这里，可以用逗号分隔
  # s.public_header_files = 'Pod/Classes/**/*.h'
  # s.frameworks = 'UIKit', 'MapKit'
  # s.dependency 'AFNetworking', '~> 2.3'
end
```

#### 添加源文件
```
生成的项目会有以下目录结构。将需要添加的源代码添加到Classes目录中。对应上面的.podspec中的s.source_files
|____TEST
| |____Assets
| | |____.gitkeep
| |____Classes
| | |____.gitkeep
| | |____ReplaceMe.m
```

#### 检测这个spec是不是配置好了
```bash
pod lib lint
```

#### 上传到服务器，并打tag
svn，git还是自己的服务器随意
这里需要先将源代码上传，再打tag。
```bash
git tag -m "注释" 1.0.0 #这里的版本号上.podspect里面保持一致
git push --tags
```

#### 检查远程配置
```
➜  BearVendor git:(master) pod spec lint

 -> BearVendor (1.0.1)

Analyzed 1 podspec.

BearVendor.podspec passed validation.
```

#### 将刚上传到服务器的仓库添加到本地`~/.cocoapod/repo`
```
pod repo add [your spec name] [your spec url]
```
执行结束以后就可以在本地的`~/.cocoapods/repos`中查看到自己的库了。

#### 使用自己的库
需要添加自己的库需要指定url，eg：
```
target 'PodDemo' do
   pod '[your spect name]', :git => '[your spec git url]'
end

```

#### 更新源代码
对于刚创建的spec库一共存在四处代码：
a. 通过`pod lib create`创建的本地项目
b. 将`a`中的代码上传到服务器的远程项目
c. `cocoa pod`从`b`这个远程项目`clone`到本地的本地项目
d. `pod install`会将`c`的项目`copy`到我们的开发工程中

so，更新源代码也是从`a->d`一步一步来了~！囧
a. 更新a中的代码
b. 上传新的代码到服务器中
c. `pod repo update [Your spec name]`将远程代码更新到`cocoa pod` 库
d. `pod update`更新，但这个命令不知道为啥好慢，so，实在不行就直接改`Podfile`，跑两次`pod install`好了

- - - -
看完上面的代码更新流程就可以发现这里有问题，怎么会这么烦琐！
接下来需要做的就是如何改进这一开发流程：
1. 在开发项目的时候也可直接改私有库；
2. 干掉上面的`d`，项目直接引用`cocoa pod`本地库文件；
然而，怎么做呢？2333，目前布吉岛~


- - - -
对于上次留下的问题，下面对其做解答。
完成上述配置步骤以后，按如下步骤进行设置：
# 一、设置静态FrameWork
1. 在前篇的私有库中添加静态framework
2. 向新生成的framework添加源代码文件和资源文件的引用（前篇中的Class和Asset文件夹）；
>BearUIFactory中包含源代码和资源文件；
>BearUIFactoryFrameWork是刚建的静态库；
>BearUIFactory.podspec是前篇中修改的；

![](Cocoa%20Pods%20%E5%88%9B%E5%BB%BA%E7%A7%81%E6%9C%89%E4%BB%93%E5%BA%93/2043160-f8df06c23065d231.png)


# 二、设置Podfile
```ruby
platform :ios, '8.0'
$repos = ENV["HOME"]+'/.cocoapods/repos/'

#main app
#这里必须，要不无法将刚生成的静态库项目添加到工程中；
workspace 'BearDS'
target 'BearDS' do
    #前篇中这里指定的是git地址，官方文档上解释说如果使用的是
    #path就可以设置成开发模式；
    pod 'BearUIFactory',:path=>"#{$repos}BearUIFactory"
end

target 'BearUIFactoryFrameWork' do
        #这里通过添加project指令将刚生成的静态库添加到主工程中
	project "#{$repos}BearUIFactory/BearUIFactoryFrameWork/BearUIFactoryFrameWork.xcodeproj"
end

# 配置 ENABLE_STRICT_OBJC_MSGSEND
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|
            config.build_settings['ENABLE_STRICT_OBJC_MSGSEND'] = 'NO'
            config.build_settings['APPLICATION_EXTENSION_API_ONLY'] = 'NO'
        end
    end
end
```
- - - -
最后来看一下项目的结构

![](Cocoa%20Pods%20%E5%88%9B%E5%BB%BA%E7%A7%81%E6%9C%89%E4%BB%93%E5%BA%93/2043160-7742c0d6185833e2.png)


先整理到这里吧，改天继续完善细节~！