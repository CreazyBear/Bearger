
```sh
$ pod install
Analyzing dependencies
Fetching podspec for `DoubleConversion` from `./RNComponent/node_modules/react-native/third-party-podspecs/DoubleConversion.podspec`
Fetching podspec for `Folly` from `./RNComponent/node_modules/react-native/third-party-podspecs/Folly.podspec`
Fetching podspec for `React` from `./RNComponent/node_modules/react-native`
Fetching podspec for `glog` from `./RNComponent/node_modules/react-native/third-party-podspecs/glog.podspec`
Fetching podspec for `yoga` from `./RNComponent/node_modules/react-native/ReactCommon/yoga`
Downloading dependencies
Installing DoubleConversion (1.1.6)
Installing Folly (2016.10.31.00)
Using React (0.57.1)
Installing boost-for-react-native (1.63.0)
Installing glog (0.3.5)
[!] /bin/bash -c 
set -e
#!/bin/bash
set -e
 
PLATFORM_NAME="${PLATFORM_NAME:-iphoneos}"
CURRENT_ARCH="${CURRENT_ARCH}"
 
......(省略)
 
xcrun: error: SDK "iphoneos" cannot be located
xcrun: error: SDK "iphoneos" cannot be located
xcrun: error: SDK "iphoneos" cannot be located
xcrun: error: unable to lookup item 'Path' in SDK 'iphoneos'
/Users/galahad/Library/Caches/CocoaPods/Pods/External/glog/2263bd123499e5b93b5efe24871be317-e8acf/missing: Unknown `--is-lightweight' option
Try `/Users/galahad/Library/Caches/CocoaPods/Pods/External/glog/2263bd123499e5b93b5efe24871be317-e8acf/missing --help' for more information
configure: WARNING: 'missing' script is too old or missing
configure: error: in `/Users/galahad/Library/Caches/CocoaPods/Pods/External/glog/2263bd123499e5b93b5efe24871be317-e8acf':
configure: error: C compiler cannot create executables
See `config.log' for more details
 
# 然后执行下面命令：
$ sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer/
# 输入mac密码
# Password:
# 重新安装
$ pod install
# 安装没有问题，工程运行OK .
```
