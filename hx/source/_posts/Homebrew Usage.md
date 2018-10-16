title: Homebrew安装使用总结
date: 2015-09-19 22:29:16
categories: Practice
tags: [tool, mac, develop]
---
homebrew是一个小型的，非商业性质的套件管理器。  
它能够自动化安装库或者软件，并且自动安装其所需要的依赖（不同于MacPorts，尽量使用系统自带的库），定制简单，上手容易，更新及时，而且是直接装在 /usr/local 下，减少很多各种 PATH 环境变量的定义（简直是开发必备啊）。

<!--more-->

### 安装
终端输入

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)”//请去reference中的官网去更新最新脚本
若安装失败，请检查安装条件：

1. Intel的芯片
2. 系统是OS X 10.6及以上
3. Xcode开发工具（xcode-select --install）
4. 已安装Bourne shell

### 卸载
终端输入

     ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"

### 常用命令
    brew help//查看帮助信息
    brew search//加要寻找的关键字
    brew install//加要安装的包名称,安装最新的包及其依赖
    brew uninstall//加要卸载的包名称，卸载它
    brew update//升级自身
    brew list//列出所有已安装的包名称
    brew outdated//列出所有过时的包名称
    brew upgrade//升级所有，加包名升级对应包（注意旧版本会保留）
    brew cleanup//清除所有旧版本，-n用以查看信息，加包名清除对应包旧版本
    brew doctor//诊断Homebrew存在的问题并给出建议
    brew info//加包名查询对应信息，不加查询已安装的包的存储信息
    brew deps//加包名查询对应依赖
    brew pin//固定某一包的版本
    brew upin//解除固定

### 二阶命令——brew cask使用
安装cask

    brew install cask
之后就可以用brew cask 尽情地享受了~

    brew cask help//查看帮助信息
懒得一个个写了，这里粘贴出来，很多都和brew命令相同，不同的是用cask是用来安装.app的

    alfred     displays note about new built-in alfred support
    audit      verifies installability of Casks
    cat        dump raw source of the given Cask to the standard output
    cleanup    cleans up cached downloads and tracker symlinks
    create     creates the given Cask and opens it in an editor
    doctor     checks for configuration issues
    edit       edits the given Cask
    fetch      downloads Cask resources to local cache
    home       opens the homepage of the given Cask
    info       displays information about the given Cask
    install    installs the given Cask
    list       with no args, lists installed Casks; given installed Casks, lists staged files
    search     searches all known Casks
    uninstall  uninstalls the given Cask
    update     a synonym for 'brew update'
    zap        zaps all files associated with the given Cask

### 高阶用法
     brew create//创建自己的Homebrew程式
     brew edit//使用 $EDITOR 编辑!
详细用法可以参考：[http://linfan.info/blog/2012/02/25/homebrew-installation-and-usage/](http://linfan.info/blog/2012/02/25/homebrew-installation-and-usage/)

_Reference:_

1. 官网地址： [http://brew.sh](http://brew.sh)
2. 命令说明： [https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Formula-Cookbook.md](https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Formula-Cookbook.md)
3. 官方文档： [https://github.com/Homebrew/homebrew/tree/master/share/doc](https://github.com/Homebrew/homebrew/tree/master/share/doc)
