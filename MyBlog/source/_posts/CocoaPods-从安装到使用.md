---
title: CocoaPods 从安装到使用
date: 2016-07-18 13:27:15
category: CocoaPods
tags: 工具

---
没用使用[CocoaPods](https://cocoapods.org/)之前：

 1. 我们需要将第三方的源代码文件复制到项目中
 2. 可能需要各种配置
 3. 需要关注依赖包的更新，并手动处理

这些活很烦、没含量、浪费时间。这些问题随着CocoaPods的出现，问题解决了！
<!--more-->
CocoaPods作为iOS开发的依赖管理工具，它的出现可以给开发者节省更多时间、免除不必要的烦恼。安装好CocoaPods之后，你只需要将用到第三方开源库、自己的私有库放到一个名为Podfile的文件中，然后在其目录下执行`pod install`。CocoaPods会将这些类库的源码下载下来，并且为你的工程设置好相应的系统依赖和编译参数。


CocoaPods 的安装
-----
Mac 下都自带 ruby ，但是经过我的实验，不同版本的 ruby 安装出的 cocoapods 的版本也是不同。我在没有更新ruby 的情况下安装的 cocoapods 是0.39.0版本，更新 ruby 之后

	ruby --version
	#ruby 2.3.1p112 (2016-04-26 revision 54768) [x86_64-darwin15]
	
当然是用0.39.0的cocoapods，也是可以正常使用的。

** 若更新 ruby **，你需要使用[Homebrew](http://brew.sh/index_zh-cn.html)。安装 Homebrew ：

	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
	
Homebrew 的简单使用：

* 搜索软件：brew search 软件名，如brew search wget
* 安装软件：brew install 软件名，如brew install wget
* 卸载软件：brew remove 软件名，如brew remove wget

** 使用 Homebrew 更新 ruby **：

	brew install ruby

** gem sources 的处理 **。ruby 的软件源 <https://rubygems.org> 因为使用的是亚马逊的云服务，所以被墙了，需要更新一下 ruby 的源，使用如下代码将官方的 ruby 源替换成国内淘宝的源：

	gem sources -l
	gem sources --remove https://rubygems.org/
	gem sources -a https://ruby.taobao.org/
	gem sources -l

** 升级 gem **

	sudo gem update --system

使用 ruby 的 gem 命令** 安装 cocoapods **:

	sudo gem install cocoapods
	pod setup			

`pod setup`执行后，会输出`Setting up CocoaPods master repo`，这一步骤是Cocoapods将它的信息下载到`~/.cocoapods`目录下，耗时会比较久。这时你只要耐心等待就好。

配置完成之后，查看 cocoapods 版本：

	pod --version
	#1.0.1 //之后版本变化，都会是 cocoapods 的最新版本
	
----
CocoaPods 的使用
-----
查找你要使用的第三方库，比如搜索 BHNetwork

	pod search BHNetwork
	#-> BHNetwork (0.0.1)
	#   离散式请求，对AFNetworking的二次封装
	#   pod 'BHNetwork', '~> 0.0.1'
	#   - Homepage: https://github.com/iBinaryOrg/BHNetwork
	#   - Source:   https://github.com/iBinaryOrg/BHNetwork.git
	#   - Versions: 0.0.1 [master repo]
	#   - Subspecs:
	#     - BHNetwork/BHNetworkLogger (0.0.1)
	#     - BHNetwork/BHNetworkProtocol (0.0.1)
	#     - BHNetwork/BHNetwork (0.0.1)


### 创建 Podfile

CocoaPods 的神奇都是起源于 Podfile 文件开始的。进入到工程的根目录，创建空白的 Podfile 文件：

	cd 你的工程根目录
	touch Podfile

### 编辑 Podfile

Podfile 的编辑工作，推荐使用 xcode 进行编写。

	platform :ios, '7.0'
	
	target :指定的Target do
	#约束布局
	pod 'Masonry', '~> 0.6.4'
	#数据请求
	pod 'BHNetwork', '~> 0.0.1'
	#极光推送
	pod 'JPush', '~> 2.1.5'
	end

**cocoapods 版本更新，Podfile文件中必须要加入target语句。**

### 执行 pod install 命令
编辑好 Podfile 文件之后。进入到工程目录下（Podfile文件也在此目录下），然后执行`pod install`命令。这时，CocoaPods就开始为我们做源码下载、配置依赖关系等工作。

	cd 你的工程根目录
	pod install
	
pod install 命令执行成功只有，工程根目录会多了`.xcworkspace`、`Podfile.lock`、`Pods`三个文件。
之后，我们的开发将是使用后缀为`.xcworkspace`的文件来开发的。

### 执行 pod update 命令
你可以更改 Podfile 文件，来调整你工程的依赖库。

	cd 你的工程根目录
	pod update

你可以使用 `pod update --no-repo-update` 来替换 `pod update`，都说替换之后，执行会快一点。

### 关于 Podfile.lock
当你执行`pod install`之后，CocoaPods 还会生成一个名为Podfile.lock的文件，`Podfile.lock` 应该加入到版本控制里面，不应该把这个文件加入到`.gitignore`中。因为`Podfile.lock`会锁定当前各依赖库的版本，之后如果多次执行`pod install` 不会更改版本，要`pod update`才会改`Podfile.lock`了。这样多人协作的时候，可以防止第三方库升级时造成大家各自的第三方库版本不一致。
