---
title: mac下安装pwntools
copyright: true
date: 2018-12-22 19:22:41
tags: 安装pwntools
categories: MAC
---
>安装pwntools遇到好多问题，浪费了一晚上时间  
>各种百度无果,最后还是谷歌找到了一些大大的blog参照着解决的  
>这里整理一下自己安装过程中的问题  
>再给出合适的pwntools安装教程  
>希望以后能让别人少踩坑  
>此处主要是mac上的安装教程，别的操作系统仅供借鉴
## 概览
* 避坑警告
* 安装pwntools
* 安装binutils(二进制工具)
* 总结

***

<!-- more -->

## 避坑警告

>**尽量不要使用pip安装!**  
>**尽量不要使用pip安装!**  
>**尽量不要使用pip安装!**  

pip安装首先需要`sudo pip2 install pwntools --user`，不然安装到后面会提示权限不足.而且由于mac自带python2.7，pip安装会把pwntools所依赖的各种库直接安装到系统路径//Library/Python/2.7/site-packages中(乱).  

**经过漫长等待**之后会需要这些问题(个人遇到的问题)   
```
matplotlib 1.3.1 requires nose, which is not installed. 
matplotlib 1.3.1 requires tornado, which is not installed.  
intervaltree 3.0.2 has requirement sortedcontainers<3.0,>=2.0, but you'll have sortedcontainers 1.5.10 which is incompatible.
```

前两者是安装matplotlib需要依赖nose和tornado，需要你自行安装
后者是mac自带sortedcontainers 1.5.10版本，需要你更新到3.0版本  

还有是需要你自行把checksec,cyclic等命令行工具加入环境变量(WTF,我怎么知道你路径装在哪里了)  

由于是mac自带的python，每一步都需要权限，每次都经过深思熟虑，深怕系统崩盘
经过步步操作，后面遇到更多的问题实在填不下去了，最后谷歌终于找到了友好的方法

***

## 安装pwntools

>强烈推荐用homebrew安装  
>homebrew是mac上不可或缺的大神器，安装方法直接用官网给的方法就行

```s
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

搞定homebrew后，安装pwntools十分**简单且迅速**  

```
brew install pwntools
```

等待片刻就安装完成，自动解决各种依赖关系以及环境变量,可在终端中直接使用checksec及cyclic等命令,不需要自己手动去额外操作。 

安装完后会在`/usr/local/Cellar/`下生成`pwntools`文件夹，不用管它，我们只需要用到这个路径  

```
/usr/local/Cellar/pwntools/3.12.1/libexec/lib/python2.7/site-packages
```

里面包含了pwntools所需要的各种`python`相关文件,终端中cd进入然后pwd复制路径

>这里需要大家自行去了解一下python的.pth文件作用

然后只需把这个文件夹添加到`python`的`sys.path`中,方法如下

```s
$ cd /Library/Python/2.7/site-packages
$ sudo vim mypkpath.pth     #进入后i进入编辑模式然后将前面的路径黏贴进来，ESC然后输入:wq即可
$ cat mypkpath.pth          #cat可以查看到里面内容
/usr/local/Cellar/pwntools/3.6.1/libexec/lib/python2.7/site-packages
```

>pwntools这样就装完了，是不是很简便

接下来测试一下

```s
$ python2.7     #本人电脑装了python3,所以pip以及python命令都需要加上版本
>>> import pwn  #from pwn import * 也可以，自行区别两种方法的不同  
```

**这步不报错你已经成功了一半了!**

报错本人碰到两种情况
1. 找不到pwn包  
先写个如下py脚本  
```python
import sys
list = sys.path
print(list)
sys.path.append('/usr/local/Cellar/pwntools/3.12.1/libexec/lib/python2.7/site-packages')
print(list)
from pwn import *
```
没有问题的话就去确认之前写的pth文件  
如果pth文件没写错的话，退出python重新试几次应该就可以了  

2. ImportError: cannot import name _remove_dead_weakref  
此问题是因为之前用brew安装过python2.7，导致brew安装的python与系统python引用产生混乱。  
删除brew安装的python2.7即可
```s
# 删除brew安装的`python@2`
brew uninstall --ignore-dependencies --force python@2
```
再次打开python，继续测试
```s
$  python2.7
>>>import pwn
>>>pwn.asm("xor eax,eax")
'1\xc0'         #输出此项即表示没问题
```
此处正常情况应该会报错，提示你需要安装二进制工具binutils

***

## 安装binutils(二进制工具)

同样使用homebrew即可快速安装
```s
$ brew install https://raw.githubusercontent.com/Gallopsled/pwntools-binutils/master/osx/binutils-$ARCH.rb
```
**关键来了** 不要盲目复制,这里$ARCH需要替换成自己电脑的架构。  
但是架构这玩意我百度半天没有一个能用的方法,最后本人使用了盲猜,现在电脑一般都是64位系统,所以盲猜了一手amd64，那么命令如下:(是的，我成功了)
```s
$ brew install https://raw.githubusercontent.com/Gallopsled/pwntools-binutils/master/osx/binutils-amd64.rb 　　
```
>建议还是自行找找查看架构的方法，~~盲猜大法好~~

**最后尝试一下pwntools测试**
```s
$  python2.7
>>>import pwn
>>>pwn.asm("xor eax,eax")
'1\xc0'     #输出成功则完结撒花
```
***

## 总结

pwntools是目前见过最难装的python库了.  
本人遇到大致就这些坑，至此算是全部填完了.  
一路上各种百度各种不靠谱，谷歌里老外的解决方法也不好使，反而是找到几个国人的blog非常有用:)这里贴一下他们的blog吧  
[pwdme.cc](http://pwdme.cc/2017/09/09/mac-install-pwntools/)  
[roytse.github.io](https://roytse.github.io/2017/07/23/%E5%85%B3%E4%BA%8EOS_X%E5%AE%89%E8%A3%85pwntools%E7%9A%84%E9%82%A3%E4%BA%9B%E5%9D%91/#%E5%AE%89%E8%A3%85%E8%BF%87%E7%A8%8B)  
别的应该也不会有什么大问题了，百度一下应该都是可以解决的
>说了这么多，不会pwn又有什么用呢:(



