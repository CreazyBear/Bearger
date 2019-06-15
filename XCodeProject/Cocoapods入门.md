# 引言

虽然平时使用pod很多，但pod到底为我们做了什么，脑子里还是一团迷雾。所以，这次简单对pod进行一个主流程跟进，不会深入分析具体实现。旨在对pod的运作有个粗略的了解。

# 工程组成
CocoaPods is composed of the following projects:

| Status | Project | Description | Info |
| :----- | :------ | :--- | :--- |
| [![Build Status](http://img.shields.io/travis/CocoaPods/CocoaPods/master.svg?style=flat)](https://travis-ci.org/CocoaPods/CocoaPods) | [CocoaPods](https://github.com/CocoaPods/CocoaPods) | The CocoaPods command line tool. | [guides](https://guides.cocoapods.org)
| [![Build Status](http://img.shields.io/travis/CocoaPods/Core/master.svg?style=flat)](https://travis-ci.org/CocoaPods/Core) | [CocoaPods Core](https://github.com/CocoaPods/Core) | Support for working with specifications and podfiles. | [docs](https://guides.cocoapods.org/contributing/components.html)
| [![Build Status](http://img.shields.io/travis/CocoaPods/cocoapods-downloader/master.svg?style=flat)](https://travis-ci.org/CocoaPods/cocoapods-downloader) |[CocoaPods Downloader](https://github.com/CocoaPods/cocoapods-downloader) |  Downloaders for various source types. |  [docs](https://www.rubydoc.info/gems/cocoapods-downloader)
| [![Build Status](http://img.shields.io/travis/CocoaPods/Xcodeproj/master.svg?style=flat)](https://travis-ci.org/CocoaPods/Xcodeproj) | [Xcodeproj](https://github.com/CocoaPods/Xcodeproj) | Create and modify Xcode projects from Ruby. |  [docs](https://www.rubydoc.info/gems/xcodeproj)
| [![Build Status](http://img.shields.io/travis/CocoaPods/CLAide/master.svg?style=flat)](https://travis-ci.org/CocoaPods/CLAide) | [CLAide](https://github.com/CocoaPods/CLAide) | A small command-line interface framework.  | [docs](https://www.rubydoc.info/gems/claide)
| [![Build Status](http://img.shields.io/travis/CocoaPods/Molinillo/master.svg?style=flat)](https://travis-ci.org/CocoaPods/Molinillo) | [Molinillo](https://github.com/CocoaPods/Molinillo) | A powerful generic dependency resolver.  | [docs](https://www.rubydoc.info/gems/molinillo)
| [![Build Status](http://img.shields.io/travis/CocoaPods/CocoaPods-app/master.svg?style=flat)](https://travis-ci.org/CocoaPods/CocoaPods-app) | [CocoaPods.app](https://github.com/CocoaPods/CocoaPods-app) | A full-featured and standalone installation of CocoaPods.  | [info](https://cocoapods.org/app)
|  | [Master Repo ](https://github.com/CocoaPods/Specs) | Master repository of specifications. | [guides](https://guides.cocoapods.org/making/specs-and-specs-repo.html)


当然其中有些只是辅助性的，比较重要的就是前面四个了。CocoaPods、CocoaPods Core、CocoaPods Downloader、Xcodeproj、CLAide、Molinillo。可以把CocoaPods当作是一个主工程，然后，把其中用的功能分拆到其它组件库中。


# 主流程

## 1. 从`Init`开始。
我们所执行的指令：`pod init` 等里面的pod就是从这个库里开始的。在这个库里有个pod文件。去掉非主流程代码（一向如此，在分析一些代码的时候，会选择直接把异常处理和分支代码直接干掉，抽出主流程，这样分析起来会简单一些。）

```ruby
require 'cocoapods'
Pod::Command.run(ARGV)
```

所以，主要就上面那两行代码。加载依赖库，执行命令。Command的`run`方法里也没啥。主要继承至`CLAide`，大概就是把pod后面的参数拿出来，做为函数再执行一下。所以，我们回到`cocoapods init.rb`中。`init`主要就是根据当面项目(Project)生成一个podfile模板页面。

```ruby
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

target 'LeanPod' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  # use_frameworks!

  # Pods for LeanPod

  target 'LeanPod3Tests' do
    inherit! :search_paths
    # Pods for testing
  end

  target 'LeanPod3UITests' do
    inherit! :search_paths
    # Pods for testing
  end

end

target 'LeanPod2' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  # use_frameworks!

  # Pods for LeanPod2

end

target 'LeanPod3' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  # use_frameworks!

  # Pods for LeanPod3

  target 'LeanPod3Tests' do
    inherit! :search_paths
    # Pods for testing
  end

  target 'LeanPod3UITests' do
    inherit! :search_paths
    # Pods for testing
  end

end

```

我在一个Project中添加了3个target，其中`LeanPod3`还带了`test`。init的功能还是相对简单的。主要是对project的解析，并生成一个podfile文件。上面也提到过，cocoapds把一些功能封装到第三方库中。对project的解析都在`xcodeproj`这个库中。有兴趣可以跟一下。
目前笔者使用的pod版本是1.5.0，可以看到这里有个问题：LeanPod3Tests和LeanPod3UITests被两个targeet当作测试单元了。这样在install的时候是会报错的。我先手动把第一个里的干掉。

## 2 `install`做了什么

初始化过后，我们添加pod库，下一步就是install了。先看一下command中install的代码。

```ruby
def run
verify_podfile_exists!
installer = installer_for_config
installer.repo_update = false
installer.update = false
installer.deployment = false
installer.clean_install = false
installer.install!
end
```

去掉非主要代码后就只有这个run方法了。（原代码中还有几个可选参数，有兴趣的可以看一下）
installer的定义在command.rb文件中。

```ruby
# ###################### 1. 创建Installer
# command.rb
def installer_for_config
    Installer.new(config.sandbox, config.podfile, config.lockfile)
end

# ###################### 2. 创建podfile对象
# config.rb
def podfile
    @podfile ||= Podfile.from_file(podfile_path) if podfile_path
end

# ###################### 3. 创建podfile对象
# command/ipc/podfile.rb
def run
    require 'yaml'
    podfile = Pod::Podfile.from_file(@path)
    output_pipe.puts podfile.to_yaml
end

# ###################### 4. 创建podfile对象
# /Core/lib/cocoapods-core/podfile.rb
def self.from_file(path)
    path = Pathname.new(path)
    unless path.exist?
    raise Informative, "No Podfile exists at path `#{path}`."
    end

    case path.extname
    when '', '.podfile', '.rb'
    Podfile.from_ruby(path)
    when '.yaml'
    Podfile.from_yaml(path)
    else
    raise Informative, "Unsupported Podfile format `#{path}`."
    end
end

# ###################### 5. 创建podfile对象
# /Core/lib/cocoapods-core/podfile.rb
def self.from_ruby(path, contents = nil)
    contents ||= File.open(path, 'r:utf-8', &:read)
    podfile = Podfile.new(path) do
    # rubocop:disable Lint/RescueException
    begin
        # rubocop:disable Eval
        eval(contents, nil, path.to_s)
        # rubocop:enable Eval
    end
    # rubocop:enable Lint/RescueException
    end
    podfile
end

# ###################### 6. 开始执行
# installer.rb
def install!
    prepare
    resolve_dependencies
    download_dependencies
    validate_targets
    generate_pods_project
    if installation_options.integrate_targets?
    integrate_user_project
    else
    UI.section 'Skipping User Project Integration'
    end
    perform_post_install_actions
end

```

config.podfile生成了一个Podfile对象，并带到Installer中，然后Installer中进行相关操作。

执行结果如下：（这次我没有加pod依赖）

```sh
➜  LeanPod git:(master) ✗ pod install --verbose
  Preparing

Analyzing dependencies

Inspecting targets to integrate
  Using `ARCHS` setting to build architectures of target `Pods-LeanPod`: (``)
  Using `ARCHS` setting to build architectures of target `Pods-LeanPod2`: (``)
  Using `ARCHS` setting to build architectures of target `Pods-LeanPod3`: (``)
  Using `ARCHS` setting to build architectures of target `Pods-LeanPod3Tests`: (``)
  Using `ARCHS` setting to build architectures of target `Pods-LeanPod3UITests`: (``)

Finding Podfile changes

Resolving dependencies of `Podfile`

Comparing resolved specification to the sandbox manifest

Downloading dependencies
  - Running pre install hooks

Generating Pods project
  - Creating Pods project
  - Adding source files to Pods project
  - Adding frameworks to Pods project
  - Adding libraries to Pods project
  - Adding resources to Pods project
  - Linking headers
  - Installing targets
    - Installing target `Pods-LeanPod` iOS 12.2
    - Installing target `Pods-LeanPod2` iOS 12.2
    - Installing target `Pods-LeanPod3` iOS 12.2
    - Installing target `Pods-LeanPod3Tests` iOS 12.2
    - Installing target `Pods-LeanPod3UITests` iOS 12.2
  - Running post install hooks
  - Writing Xcode project file to `Pods/Pods.xcodeproj`
    - Generating deterministic UUIDs
  - Writing Lockfile in `Podfile.lock`
  - Writing Manifest in `Pods/Manifest.lock`

Integrating client project

Integrating target `Pods-LeanPod` (`LeanPod.xcodeproj` project)

Integrating target `Pods-LeanPod2` (`LeanPod.xcodeproj` project)

Integrating target `Pods-LeanPod3` (`LeanPod.xcodeproj` project)

Integrating target `Pods-LeanPod3Tests` (`LeanPod.xcodeproj` project)

Integrating target `Pods-LeanPod3UITests` (`LeanPod.xcodeproj` project)
  - Running post install hooks
    - cocoapods-stats from `/Users/xiongwei/.rvm/gems/ruby-2.4.0@global/gems/cocoapods-stats-1.0.0/lib/cocoapods_plugin.rb`

Sending stats

-> Pod installation complete! There are 0 dependencies from the Podfile and 0 total pods installed.

```

整个过程大概分为以下几步（人家代码功能函数写得很清楚了 =。=）：
1. prepare:可以理解为环境检查
2. resolve_dependencies:解析 Podfile 中的依赖
3. download_dependencies:下载依赖
4. generate_pods_project:创建 Pods.xcodeproj 工程
5. integrate_user_project:集成 workspace

### 2.1 解析 Podfile 中的依赖

依赖的解析是一个相当复杂的过程，依赖的依赖，相互之间可能形成一种网状的关系。cocoapods使用Molinillo对依赖进行管理。这里不对算法进行深入解析了。

### 2.2 下载依赖

下载过程略过了，有兴趣的可以分析一下源代码。
大部分的依赖都会被下载到 `~/Library/Caches/CocoaPods/Pods/Release/`这个文件夹中，然后从这个这里复制到项目工程目录下的 `./Pods` 中。
那我们平常使用的`/Users/username/.cocoapods/repos/master`这个文件夹是干啥子的呢? 看下目录对应的git运程链接就可以看到`https://github.com/CocoaPods/Specs.git`这个就是组成部分之一的`Master Repo`一项。

官方对这个目录的描述：
> The Specs Repo is the repository on GitHub that contains the list of all available pods. Every library has an individual folder, which contains sub folders of the available versions of that pod.

可以理解为一个pod库的目录。更详细的内容可以查看：https://guides.cocoapods.org/making/specs-and-specs-repo.html

*如何创建一个自己的Spect（不是私有库），类似Spect镜像？* [搭建自己的Spect库](./搭建自己的Spect库.md)

### 2.3 创建 Pods.xcodeproj 工程

```rb
# Generates the Xcode project(s) that go inside the `Pods/` directory.
#
def generate_pods_project
  ...
  create_and_save_projects(pod_targets_to_generate, 
                          aggregate_targets_to_generate,
                          cache_analysis_result.build_configurations, 
                          cache_analysis_result.project_object_version)
  ...
end

# SinglePodsProjectGenerator
def generate!
  project_path = sandbox.project_path
  platforms = aggregate_targets.map(&:platform)
  project_generator = ProjectGenerator.new(sandbox, project_path, pod_targets, build_configurations,
                                            platforms, project_object_version, config.podfile_path)
  project = project_generator.generate!
  install_file_references(project, pod_targets)

  pod_target_installation_results = install_all_pod_targets(project, pod_targets)
  aggregate_target_installation_results = install_aggregate_targets(project, aggregate_targets)
  target_installation_results = InstallationResults.new(pod_target_installation_results, aggregate_target_installation_results)

  integrate_targets(target_installation_results.pod_target_installation_results)
  wire_target_dependencies(target_installation_results)
  PodsProjectGeneratorResult.new(project, {}, target_installation_results)
end
```

以上是主流程代码，有兴趣可以详细看一下，我是看不下去了。=。=
主要完成以下四件事：
  1. 生成 Pods.xcodeproj 工程
  2. 将依赖中的文件加入工程
  3. 将依赖中的 Library 加入工程
  4. 设置目标依赖（Target Dependencies）

代码看得有点晕。先看下cocoapods为我们创建了哪些文件吧~！

```
.
├── AFNetworking
│   ├── AFNetworking
│       └── .......
├── Headers
│   ├── Private
│   │   └── AFNetworking
│   │       └── ...... -> ../../../AFNetworking/UIKit+AFNetworking/UIWebView+AFNetworking.h
│   └── Public
│       └── AFNetworking
│           └── ...... -> ../../../AFNetworking/UIKit+AFNetworking/UIWebView+AFNetworking.h
├── Local\ Podspecs
├── Manifest.lock
├── Pods.xcodeproj
│   ├── project.pbxproj
│   ├── project.xcworkspace
│   │   ├── contents.xcworkspacedata
│   │   ├── xcshareddata
│   │   │   └── IDEWorkspaceChecks.plist
│   │   └── xcuserdata
│   │       └── xiongwei.xcuserdatad
│   │           ├── UserInterfaceState.xcuserstate
│   │           └── WorkspaceSettings.xcsettings
│   └── xcuserdata
│       └── xiongwei.xcuserdatad
│           └── xcschemes
│               ├── AFNetworking.xcscheme
│               ├── Pods-LeanPod.xcscheme
│               └── xcschememanagement.plist
└── Target\ Support\ Files
    ├── AFNetworking
    │   ├── AFNetworking-dummy.m
    │   ├── AFNetworking-prefix.pch
    │   └── AFNetworking.xcconfig
    └── Pods-LeanPod
        ├── Pods-LeanPod-acknowledgements.markdown
        ├── Pods-LeanPod-acknowledgements.plist
        ├── Pods-LeanPod-dummy.m
        ├── Pods-LeanPod-frameworks.sh
        ├── Pods-LeanPod-resources.sh
        ├── Pods-LeanPod.debug.xcconfig
        └── Pods-LeanPod.release.xcconfig

20 directories, 93 files
```

1. 依赖库源文件和头文件就不用多说了。但为啥有个public和private的header呢？还一样的？不解~！
2. `Local Podspecs`保存了本地库的spect文件。有啥用呢？
3. `Manifest.lock`检验用的
  
    project.xcproj中会对podfile.lock和这个文件进行校验，如果不一样的话，会报错。

    ```
    shellScript = "diff \"${PODS_PODFILE_DIR_PATH}/Podfile.lock\" \"${PODS_ROOT}/Manifest.lock\" > /dev/null\nif [ $? != 0 ] ; then\n    # print error to STDERR\n    echo \"error: The sandbox is not in sync with the Podfile.lock. Run 'pod install' or update your CocoaPods installation.\" >&2\n    exit 1\nfi\n# This output is used by Xcode 'outputs' to avoid re-running this script phase.\necho \"SUCCESS\" > \"${SCRIPT_OUTPUT_FILE_0}\"\n";
    ```

    > Podfile.lock：这是CocoaPods创建的最重要的文件之一。它记录了需要被安装的pod的每个已安装的版本。如果你想知道已安装的pod是哪个版本，可以查看这个文件。推荐将Podfile.lock文件加入到版本控制中，这有助于整个团队的一致性。
    
    > Manifest.lock:这是每次运行pod install时创建的Podfile.lock文件的副本。如果你见过“沙盒文件和Podfile.lock文件不同步”的错误，这个错误就是因Manifest.lock文件和Podfile.lock文件不一样引起。由于Pods所在的目录并不总在版本控制之下，这样可以保证开发者运行app之前都能更新他们的pods，否则app可能会crash，或者在一些不太明显的地方编译失败

4. [Pods.xcodeproj](./xcodeproj文件解析.md)
5. Target Support Files

### 2.4 集成 workspace

在2.3中创建了Pods.xcodeproj工程。然后再创建了一个workspace，把新的pod.xcodeproj和原来的工程LeanPod.xcodeproj都包在新的workspace中。当然，还有很多其它工程上的改动，这里就不细说了。

# 总结

只是很粗略的跟了一下主流程，cocoapods项目的流程还是很清晰的。几个常的pod指令其实后面可以接很多的参数。关于参数的作用，在描述中已经写得很清楚了，当然，我们还可以去源代码中看一下。cocoapods更多的是对工程文件进行操作。所以，要理解cocoapods是如何工作的，需要对工程文件本身要有足够的认知。


