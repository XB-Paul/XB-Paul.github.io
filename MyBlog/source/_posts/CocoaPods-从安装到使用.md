---
title: CocoaPods 从安装到使用
date: 2016-07-18 13:27:15
category: CocoaPods
tags: 工具

---
CocoaPods是Swift和Objective-C Cocoa项目的依赖管理器。目前拥有超过76000个库，在超过300万个应用程序中使用。CocoaPods可以帮助你在iOS项目中统一管理第三方开源库。

没用使用[CocoaPods](https://cocoapods.org/)之前：

 1. 我们需要将第三方的源代码文件复制到项目中
 2. 可能需要各种配置
 3. 需要关注依赖包的更新，并手动处理

这些活很烦、没含量、浪费时间。这些问题随着CocoaPods的出现，问题解决了！
<!--more-->
CocoaPods作为iOS开发的依赖管理工具，它的出现可以给开发者节省更多时间、免除不必要的烦恼。安装好CocoaPods之后，你只需要将用到第三方开源库、自己的私有库放到一个名为Podfile的文件中，然后在其目录下执行`pod install`。CocoaPods会将这些类库的源码下载下来，并且为你的工程设置好相应的系统依赖和编译参数。


CocoaPods 的安装
-----

- ### (前提)Homebrew：macOS软件包的管理器

    [Homebrew官网](https://brew.sh/index_zh-cn)
    
  - **有vpn的用户：**
	  - 安装
        
        ```
        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
        ```
      - 卸载
          
        ```
        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall.sh)"
        ```

  - **没有vpn的用户：**
    
    1. 创建Homebrew文件夹（若已存在/usr/local/Homebrew，先将其删除）
       
		```
		sudo mkdir /usr/local/Home-brew
		```
    2. git clone国内的brew镜像包

		```
		sudo git clone https://mirrors.ustc.edu.cn/brew.git /usr/local/Homebrew
		或者 
		sudo git clone https://mirrors.aliyun.com/homebrew/brew.git /usr/local/Homebrew
		或者 
		sudo git clone https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git /usr/local/Homebrew
		```
    3. 创建一个快捷方式到/usr/local/bin目录(若/usr/local/bin文件夹中已有brew，先将其删除)
      
		```
		sudo ln -s /usr/local/Homebrew/bin/brew /usr/local/bin/brew
		```
    4. 创建homebrew-core文件夹

		```
		sudo mkdir -p /usr/local/Homebrew/Library/Taps/homebrew/home-brew-core
		```
    5. git clone国内的homebrew-core镜像包
        
		```
		sudo git clone https://mirrors.ustc.edu.cn/homebrew-core.git /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
		或者 
		sudo git clone https://mirrors.aliyun.com/homebrew/homebrew-core.git /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
		或者 
		sudo git clone https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
		```
    6. 配置权限 + 更新

		```
		sudo chown -R $(whoami) /usr/local/Homebrew
		brew update
		```
	7. 设置Homebrew端口
            
		```
		echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc 
		echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
		```
    8. OK! 可尝试自检
        
		```
		brew doctor
		```
      
        
>     Homebrew 的简单使用：
>      搜索软件：brew search 软件名，如brew search wget
>      安装软件：brew install 软件名，如brew install wget
>      卸载软件：brew remove 软件名，如brew remove wget


- ### (前提)Ruby

    [Ruby官网](https://www.ruby-lang.org/)
    
    - 查看当前Ruby版本
    
        ```
        ruby -v
        ```
    - 升级Ruby环境
        
        ```
        curl -L get.rvm.io | bash -s stable 

        source ~/.bashrc
        
        source ~/.bash_profile
        ```
    - 查看可安装的版本
        
        ```
        rvm list known
        ```
    - 选择一个Ruby版本进行安装（我一般选择倒数第二个版本）
            
        ```
        rvm install x.x.x
        ```
    - 设置默认版本
    
        ```
        rvm use x.x.x --default
		```
    - 更新Gem + 更换源
        
        ```
        sudo gem update --system
        
        gem sources -l
        
        gem sources --remove 当前的源
        
        gem sources --add https://gems.ruby-china.com/
        ```
        
- ### CocoaPods 
    
    [CocoaPods官网](https://cocoapods.org)

    - 安装CocoaPods
    
        ```
        sudo gem install -n /usr/local/bin cocoapods
		```
    - clone repo库
    
        方式一：官方的方法（速度很慢）
        
        ```
        pod setup
        ```
        方式二：直接clone国内的镜像
        
        新版的CocoaPods不允许用pod repo add直接添加master库，可执行
         
        ```
        cd ~/.cocoapods/repos
        pod repo remove master
        git clone https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git
        ```
        这种方式，在工程中的podfile文件第一行加上：
        ```
        source 'https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git'
        ```
    - Done！恭喜你！
       最后，再贴一下造福国人的镜像站：
       - [清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/)
       - [中国科学技术大学开源软件镜像](https://mirrors.ustc.edu.cn)
       - [阿里云官方镜像站](https://mirrors.aliyun.com)
	
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


-------

言子玉 · 出品   @2020-09-17

仰望星空，让泪水倒流



