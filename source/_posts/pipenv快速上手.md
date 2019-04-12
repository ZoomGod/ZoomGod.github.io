---
title: pipenv快速上手
copyright: true
date: 2019-04-13 00:20:26
tags: pipenv快速上手
categories: python
---
>pipenv是目前为止最好用的python虚拟环境和包管理工具 
>当你开始需要用python完成一些稍大一点的项目时，你就需要学会使用pipenv了 
## 概览 
* 需要做的准备 
* 为什么需要使用pipenv 
* 快速上手使用 
* 了解pipenv的运行机制 
* 后话

*** 
<!-- more --> 
## 需要做的准备 
**[+] 安装好python(最好是3x版本)**
**[+] 官方显示支持python2.7&python3.4及以上版本** 

*** 
## 为什么需要使用pipenv 
*大家电脑中一般都会很自然的去安装最新版本的各种库，但是实际使用中有些开发环境需要使用到旧版本(一般是稳定的版本)。如果每次都降版本是个很麻烦的事，可能会影响到你平时的使用。所以需要虚拟环境来应对各种要求。*
>ps:就是要优雅，不要让自己的电脑被弄的一团糟 

*** 
## 快速上手使用 
>一切操作都只能在文件夹内使用: ) 

### 安装pipenv 
```sh 
pip3 install pipenv
brew install pipenv         #mac也可以使用这种方法，官方推荐
```

### 创建一个文件夹来建立你的虚拟环境
```sh 
mkdir xxx_pipenv            #名称随便，但是做好管理很重要
cd xxx_pipenv
pipenv install
#可以使用 pipenv install --python x.x 来指定使用的python版本，前提你电脑里有
```

>这时文件夹内会多出pipfile和pipfile.lock两个文件，用处参考后面的运行机制

### 安装所需要的库
```sh
pipenv install flask             #安装最新版本的库及其依赖包
pipenv install flask==xxx        #安装指定版本
```

### 查看已安装的库及其依赖图
```sh
pipenv graph
```

### 使用虚拟环境运行py文件
```sh
pipenv run python xxx.py
#使用pipenv shell可以激活虚拟环境shell，可以省去pipenv run前缀
```

### 检查安全性
```sh
pipenv check            #感觉没什么用，but每次安装完运行一下会显得你比较专业
```

### 删除不需要的库
```sh
pipenv uninstall xxx
```

### 移除虚拟环境
```sh
pipenv --rm
```

### 一些查询语法
```sh
pipenv --where          #效果等同于pwd，查看当前路径
pipenv --venv           #显示虚拟环境路径
pipenv --py             #显示虚拟环境使用的python路径
pipenv --bare           #精简操作手册
```

>还有一些命令感觉并不常用，就不写了，想了解的可以pipenv --bare查看
>当然最全的就是去查看官方文档啦

*** 
## 了解pipenv的运行机制 
>pipenv的核心围绕着pipfile和pipfile.lock这两个文件 
>创建pipenv时会判断文件夹内是否存在这两个文件，不存在会自动创建
>若存在则会读取内容，因此可以拿来移植给他人复现你的虚拟环境

**首先来看pipfile这个文件**
```
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]
flask = "*"

[requires]
python_version = "3.7"
```

首先看`[source]`
别的无关紧要，url这边一眼就可以看出是对应的pypi源，官方源比较慢，可以换成国内源，如下修改即可
```
url = "https://pypi.tuna.tsinghua.edu.cn/simple"
```

然后是`[dev-packages]`
意为开发-包。在你把pipfile给别人时，这里面记录的包不会被自动安装，一般用来放一些开发是用到的辅助测试之类的库(猜测)
```sh
pipenv install xxx --dev        #安装时记录进[dev-packages]
```

那么`[packages]`就很好理解了，前面我们示例安装了flask最新版，packages中就记录了这一点，其他人使用你的pipfile时也会去下载对应库及版本

最后是`[requires]`，里面记录了基础需求，也就是会被必然下载的部分 

>此时会有一个疑问:我安装的flask目前是最新的，但是移植给别人时可能已经不是最新的了，这不就会出问题嘛。这时pipfile.lock的作用就来了

`pipfile.lock`中记录了你安装的所有的库及其版本号的hash，在新的环境中安装时会去匹配hash，这样就可以保证每次安装出来的环境是一样的

*** 
## 后话
pipenv的使用其实还是非常简洁易上手的，然而我学习使用时还是依靠百度了N个网页233(太多不靠谱的了！)
然后还是推荐配合pycharm来使用，不然每次都要pipenv run python xxx 或者pipenv shell开虚拟环境shell。这种事情...还是交给IDE来做就好啦

