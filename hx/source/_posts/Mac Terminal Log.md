title: 【Mac】Terminal改造小记
date: 2016-09-21 02:35:22
categories: Practice
tags: [tool,mac,develop]
---
先上主要配置，想要自己折腾的同学自己可以行动起来了。

配置推荐：**HyperTerm**(`hyperterm-snazzy`+ `hyperpower` + `hyperpower-plus` + `hyperline`)+**oh-my-zsh**(`pure`+`git`+`autojump`+`osx`+`zsh-syntax-highlighting`+`zsh-wakatime`)+**Tmux**

这里是显示效果：

![](http://7xkwu7.com1.z0.glb.clouddn.com/HyperTerm.png)

<!--more-->


电脑信息的显示用的命令`archey`，这样每次启动zsh时就能了解现在系统的大致情况了（好吧，然并软）。用`brew install archey`后直接在`~/.zshrc`加一行`archey`即可。

## HyperTerm配置

HyperTerm是基于JS/HTML/CSS写的terminal，所以效果各种狂拽炫酷吊炸天，可惜还不够稳定，我认为做到类似的像编辑器中的atom那样就差不多了，但这个项目估计也没这么大号召力...

也没多折腾，就在配置的plugins里加了几个有(zhuang)用(bi)的插件。

- 编辑`/Users/yourname/.hyperterm.js`
- 在`config`里修改默认字体为16
- 在`config`里加入windowSize: '[100, 30]'
- 我的`plugins`栏加入`hyperterm-snazzy`, `hyperpower`, `hyperpower-plus`, `hyperline`，第一个是主题，第二个让输入带特技，第三个...让输入更震撼！！！第四个就是底下的状态栏，各位已经看到了。

其实只是配置这些还达不到图中的效果，还要配置zsh的主题和一些命令高亮

好嘛，配置的挺好看，最后我还是用回了terminal，HyperTerm还是不够稳定，也不支持我的一些像Go2Shell这样的小插件，对我这台破电脑也不大友好（各位看看CPU利用率）...

好在zsh和Tmux配置好了就可以到处跑了，给自带终端加个不错的主题后，我们就把终端的选择先放一边吧。
放上我的主题

## zsh配置小记

要用zsh怎么能不装oh-my-zsh呢（其实也就是因为它zsh才火起来的）？

- wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh 自动安装
- 在`~/.zshrc`下编辑你的配置就行了，各位别忘了把terminal的启动命令换成zsh


接下来是我的一些个性化配置


- 我用的是pure主题，oh-my-zsh已经把它加入默认的主题包里，你把默认的`ZSH_THEME="robbyrussell"`改成`ZSH_THEME="pure"`即可
- 但这不是最新的配置，可以去[pure](https://github.com/sindresorhus/pure)下载最新的主题，注意不要和原先的冲突（好吧，HyperTerm也是它告诉我的）


- 要高亮命令，需要`brew install zsh-syntax-highlighting`，然后在插件里加上`zsh-syntax-highlighting`就好了；

- 要配置`wake-time`，需要sudo pip install wakatime.
- cd ~/.oh-my-zsh/custom/plugins && git clone https://github.com/wbinglee/zsh-wakatime.git

- 然后在插件里加入zsh-wakatime就好了，过段时间就可以看统计信息了

![](http://7xkwu7.com1.z0.glb.clouddn.com/wakatime_for_terminal.png)
- 你还可以加入autojump、osx这些小插件扩展，虽然感觉我装了也没怎么用...

现在你的terminal就可以有我上图给的那样的效果了。

## Tmux 使用小记

tmux是个终端复用的工具，我看重它主要好用的地方有：

- 分屏功能
- 现场恢复（未重启）


安装直接`brew install tmux`即可，简单粗暴。

关于tmux的使用教程网上有很多，有些讲的挺透彻的，这里主要还是记录一些我个人常用的。

使用：

- tmux new -s session //用session为名创建一个新会话
- tmux a -t your_session_name //恢复名为your_session_name的会话

接下来讲讲它的配置，我换了一圈流行配置感觉还是下面这个比较搭我的terminal，其他的感觉都太臃肿太复杂了，github上star数最高那个安装就挺麻烦的...

![](http://7xkwu7.com1.z0.glb.clouddn.com/tmux_config.png)

配置文件：
     去https://github.com/zanshin/dotfiles/tree/master/tmux下载配置文件
     重命名为.tmux.conf放在你的~目录下
     用brew install reattach-to-user-namespace解决在mac下的一些问题
     用tmux source-file ~/.tmux.conf来启动你的配置

其中一些常用配置整理：

- prefix改成了`
- `prefix+|`左右分栏
- `prefix+-`上下分栏
- 默认开启鼠标选择pane模式


还有挺多小功能的，像显示正在访问的ip啊什么的，目前我也是用一些学一些，下面贴上我学习的一些功能。

---
### 这里是功能更新区（据说是长期）

复制粘贴：

* prefix + [ 启动“copy-mode”、
* 像在vim下一样移动到你想去的那一行
* 使用v开始选择
* 像在vim一样选择你需要的内容，被选用的内容会被高亮
* 使用y复制所选内容
* 这样内容就在你粘贴板上了
* 你可以用pbpaste来查看刚刚复制的内容

来自https://robots.thoughtbot.com/tmux-copy-paste-on-os-x-a-better-future

----


虽然时常还有些小bug，但tmux真的非常好用，可以说网络编程必备，平时经常和服务器打交道的小伙伴建议也学习一个。

怎么样，有没有觉得terminal也很好玩呐~
