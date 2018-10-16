title: Markdown 学习
date: 2015-08-05 20:22:48
categories: Practice
tags: [web]
---
刚建好自己的小窝，第一篇正式的博文记录了自己学习的Markdown语法，毕竟以后都要使用它来写博客，大家多多指教。
<!--more-->

### 斜体设置

你需要在需要斜体的部分前后加上一个underscore（下划线），像_这样_。

### 粗体设置

你需要在需要粗体的部分都加上两个asterisks（星号）,像**这样**。
这里有个小插曲，我发现加一个asterisks是*这样*，也是斜体。

**以上两种语法的组合不分先后.**
### 行内代码
你需要使用反引号 (\`)(在键盘上位于1的左边)把文字括起来来表示`行内代码`
### 标题设置

你需要在你的标题前面加若干个连续的hash mark（俗称井号），井号越多字体越小，最多六个，像这样：
# 重要的事情说三遍
## 重要的事情说三遍
### 重要的事情说三遍
#### 因为很重要所以再说三遍
##### 因为很重要所以再说三遍
###### 因为很重要所以再说三遍
	注意hash mark只有在行首才有用，否则会失去它本身的作用而被无情地显示出来T_T
只要hash mark在行首，对后面的内容部分你可以随意地用上面介绍的两个语法让文字_变斜_，或者**加粗**。
	还有一个要提醒大家的地方就是——**慎用**一号标题和六号标题！！！

以上的标题设置我们称之为**类atx**形式的语法，接下来要介绍另一种局限性比较大的标题设置语法——**类Setext**形式语法。
类 Setext 形式是用底线的形式，利用 =（最高阶标题）和 -（第二阶标题），任意数量的 = 和 - 加在要作为标题的文本下方即可。

### 分隔线

你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。
### 网页链接

在Markdown的语法里有两种链接网页的方式：
##### 第一种：Inline Link
在你想显示的文本外边框起brackets（方括号），紧接着在后边用parenthesis（圆括号）框起这段文字指向的链接，例如：
	[千万不要点这里](http://mhxie.me)  
Remark:  

* brackets和parenthesis都是半角的;
* 还有前两种语法对brackets里的内容也适用;
* 标题里也可以添加链接;

##### 第二种：Reference Link
正如Reference的意思，这种链接网页的方式实际上是对文档内其他位置的链接的引用。它的语法是在链接文字的括号后面再接上另一个方括号，而在第二个方括号里面要填入用以辨识链接的标记：

	This is [an example][id] reference-style link.
你也可以选择性地在两个方括号中间加上一个空格：

	This is [an example] [id]reference-style link.

接着，在文件的任意处，你可以把这个标记的链接内容定义出来：

	[id]: http://example.com/  "Optional Title Here"

链接内容定义的形式为：

* 方括号（前面可以选择性地加上至多三个空格来缩进），里面输入链接文字
* 接着一个冒号
* 接着一个以上的空格或制表符
* 接着链接的网址
* 选择性地接着 title 内容，可以用单引号、双引号或是括弧包着

下面这三种链接的定义都是相同：

	[foo]: http://example.com/  "Optional Title Here"
	[foo]: http://example.com/  'Optional Title Here'
	[foo]: http://example.com/  (Optional Title Here)


这样做的一个好处是，当多个link指向同一个网址时，更新时只需要修改一个地方，方便也不容易忘记。
##### 第三种：Auto Link
Markdown 支持以比较简短的自动链接形式来处理网址和电子邮件信箱，只要是用方括号包起来， Markdown 就会自动把它转成链接。一般网址的链接文字就和链接地址一样。
### 插入图片

当你已经学会插入网页链接时，插入图片也不是什么难事了，插入图片也有两种方式，要做的只是把先前网页链接的部分换成图片的外链地址，为了区别于网站链接而将图片显示出来，在方括号前面加上一个exclamation point（叹号）即可
![A representation of Octdrey Catburn](http://7xkwu7.com1.z0.glb.clouddn.com/doge_sleep_icon.png)

但是非常可惜的是，这种方法并不能调整图片的长宽，想要调整，还是得学习一点相关的Html语法。
### Blockquote（引用区块）

引用区块就是利用一句话或者一个段落的特殊格式来引起读者注意的语法。要做到这件事情也是十分简单，只要在要引用的句子或段落的前面加上"greater than" caret（大于号）就可以了，例如：
>If you need to call special attention to a quote from another source, or design a pull quote for a magazine article, then Markdown's blockquote syntax will be useful. A blockquote is a sentence or paragraph that's been specially formatted to draw attention to the reader.
>
To create a block quote, all you have to do is preface a line with the "greater than" caret.

Blockquote还支持嵌套（例如：引用内的引用），只要根据层次加上不同数量的 >

### Codeblock（代码区块）

在Markdown里添加代码也非常简单，只需要缩进一个table（制表符）或者四个space（空格）就好了。  
注意代码区块里斜体加粗引用等Markdown语法都会失效。

	#inluce<iostream>
	using namespce std;

	int main(){
		cout << "Hello, World!" << endl;
		return 0;
	}
### 创建列表

##### 第一种：Unordered List（无序列表）
It's easy.在每一项的前面加上asterisk（星号）就好，记得星号后面要留一个space（空格）或table（制表符）哦，**还有一个特殊的地方就是在这里plus sign（加号）和minus sign（减号）可以与asterisk混用。**
##### 第二种：Ordered List（有序列表）
有序列表就是数字加上一个半角句点和一个space或table就好了。但是要注意不小心产生有序列表的情况，所以尽量避免数字半角句点和space/table连续出现的情况，实在无法避免在句点前加上一个backslash（反斜杠）即可——**_反斜杠通常被利用来来插入一些在语法中有其它意义的符号(又被称作转义符)_**.

Remark：在实践中，我发现一个有趣的现象，就是有序列表只要制定了开头，后边的序号可以不用写，用asterisk/minus sign/plus sign代替也是可以，这样的话它会自动递增地帮你打印出序号来。
##### 多级列表的处理
在新一级的标识符前加上table来缩进即可，打印出来的效果像这样

* First level
	* Second level
		* Third level
			* Fourth level

### 创建表格
这个并不是**Markdown**的标准语法，似乎需要解释器，先看看能不能发上来。  
示例：

	H1 |H2 |H3
	---|---|---
	0,0|0,1|0,2
	1,0|1,1|1,2
	2,0|2,1|2,2

H1 |H2 |H3
---|---|---
0,0|0,1|0,2
1,0|1,1|1,2
2,0|2,1|2,2


### 段落和换行

你在实际操作中会发现，即使你在编辑时输入return（回车），显示出来的仍旧是一行，在Marhdown语法里也有两种方式来实现换行：
##### Hard Break
直接再输入一个return（回车），这样段落的间距比较大。
##### Soft Break
在段末输两个连续的space（空格），这样段落的间距会比较小。

总的来说，Markdown语法还是十分简单易学的，还有就是建议大家还是在实践中学习会比较快。  
最后，感谢这个Markdown教程的网站，它提供了训练式的Markdown学习，对我的帮助十分大。[http://markdowntutorial.com/](http://markdowntutorial.com/)也十分感谢[_Markdown 语法说明 (简体中文版)_](http://www.appinn.com/markdown/#img)告诉我许多其他的语法作为补充
