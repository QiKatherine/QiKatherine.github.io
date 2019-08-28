+++
title = "Best workaround to use Emacs in MS Windows | 在MS windows中使用Emacs的最佳解决方案"
date = 2019-08-16T01:03:00+01:00
lastmod = 2019-08-28T23:56:10+01:00
tags = ["Emasc", "msys2"]
categories = ["TECH"]
draft = false
+++

## Background {#background}

Due to the working environment limitation, I occasionally have to use MS windows system (and therefore Emacs for Windows). But some similar users and I have constantly found cases where Emacs is significantly relying on \*unix system. So far, my experience is that compling Emacs in msys2 has been a best (maybe) workaround in this situation. If you this is relatable to you, you might want to give it a try:
<https://chriszheng.science/2015/01/23/Guideline-for-building-GNU-Emacs-with-MSYS2-MinGW-w64/>
There has been ample discussion online, so I will be writting in Chinese. If you are interested in the trouble shootings below, try google translate. It will be fun :).

我因为工作的原因有时候不得不使用Windows。Emacs for Windows，在这种情况下，已经是一个比较合适的选择了，我用了半年多对它各方面都还相对满意。但是总不时会发现，Emacs里面还有很多严重依赖\*Unix system的操作。为了找到一劳永逸的办法（做梦），我尝试过cygwin, mingw64还做了一些功课，目前发现最好方式是，将整合到msys2里面使用，或者使用msys2编译的Emacs。


## MSYS2 {#msys2}

MSYS2是MS-Windows下编译自由/开源软件的一个环境，衍生自Cygwin，也就是说它和Cygwin一样，编译出的程序不能脱离Cygwin环境运行(其实就是离不开那几个DLL文件)。但MSYS2有一个很牛的地方是它自带了MinGW-w64，MinGW-w64可以认为是MinGW的升级版本，编译出的程序是原生的Windows程序，最大的特点和名字一样，支持编译出64位的程序。目前MSYS2和MinGW-w64开发都很活跃，两者结合，既发挥了MSYS2对\*NIX世界的兼容性，又能用MinGW-w64编译原生代码，很爽，自带的包很丰富，包管理采用Arch Linux用的Pacman，非常的方便。

据说msys2目前是提供最多类Unix开发工具的环境，而且为想尝试\*unix的windows users整体上提供了十分优秀的模拟环境。

Msys2的下载安装都很简单，参照管网指南操作即可。中文用户配置可以参考以下链接：
<https://zhuanlan.zhihu.com/p/33751738>
<https://zhuanlan.zhihu.com/p/33789023>


## Compiling Emacs {#compiling-emacs}

_在msys2里面安装最简单的是使用pacman -S Emacs，安装完的版本在c:/msys2/usr/bin里，dotfile在c:/msys2/home/user_.emacs.d下方，我试图运行内置function，正常，但是使用dotfile加载同样的function总显示加载错误。/

而且chris老师提到Windows下使用emacs最好的方式还是用自己编译的Emacs，所以我也选择这么做。自编译Emacs要安装一系列libraries，然后从原代码git.sv.gnu.org/emacs.git从这里clone所有的东西下来，按下列文章一步一步编译
<https://emacs-china.org/t/topic/3276/13>
<https://chriszheng.science/2015/03/19/Chinese-version-of-Emacs-building-guideline/>
<http://git.savannah.gnu.org/cgit/emacs.git/tree/nt/INSTALL.W64>

这个安装包都是为了在msys2中编译Emacs而写，所以安装途中不需要由什么特别改动的地方，注意一步一步执行代码就好。还有一点不得不提，Gti自动改换行符的功能(autocrlf)很讨厌，会造成各种意想不到的神仙bug（e.g. 很多人猜测这个也是造成spacemacs版本的font-lock+ error的原因）我们用下面的命令关掉它：
$ git config core.autocrlf false
`Update: 新版的git已经默认这项是关闭了。如果有需要，可以安装完后再把值改回true，一直默认关闭会导致有些git操作持续return warning，泪目。`

安装时需要一些依赖库，如果你的系统里面MSYS2已经被添加到PATH环境变量里(例如PATH里包含了C:\msys2\mingw64\bin)，就不用从mingwin64/bin里面复制必用的libraries去c:/emacs1/bin了，所以直接在PATH里添加环境会比较方便。


## Advantages {#advantages}

跟以前使用的Emacs for MS Win64一样，emacs配置文件还是默认在C:/Users/AppData/Roaming/.emacs.d中。大多数package放在本地c:/msys2/home/user/.emacs.d/elpa/yourdir/以后使用 `(add-to-list 'exec-path "yourdir")` 即可正常调用。

但是我感觉从运行速度来讲，msys2 compiled Emacs比Emacs for Win64 `快很多` 。所以在win中使用Emacs，虽然也有WSL或者VM based的解决方案，但是msys2（在许多人看来）仍然是一个在win环境中使用类unix系统的优秀途径，希望未来能研究编译过的emacs在msys2提供的类unix系统里是否和其他libraries有更好的互动。
