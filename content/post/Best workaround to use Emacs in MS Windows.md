+++
title = "Best workaround to use Emacs in MS Windows 【在 MS windows 中使用 Emacs 的最佳解决方案】"
summary = "Experience Emacs with best workaround environment in windows."
date = 2019-08-16T01:03:00+01:00
lastmod = 2020-06-23T02:02:54+01:00
tags = ["Emasc", "Msys2", "Windows10"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

## Background {#background}

---

Due to the working environment limitation, I occasionally have to use MS windows system (and therefore Emacs for Windows). But some similar users and I have constantly found cases where Emacs is significantly relying on \*unix system. So far, my experience is that compiling Emacs in msys2 has been a best (maybe) workaround in this situation. If you this is relatable to you, you might want to give it a try:
<https://chriszheng.science/2015/01/23/Guideline-for-building-GNU-Emacs-with-MSYS2-MinGW-w64/>
There has been ample discussion online, so I will be writing in Chinese. If you are interested in the trouble shootings below, try google translate. It will be fun :).

我因为工作的原因有时候不得不使用 Windows。Emacs for Windows，在这种情况下，已经是一个比较合适的选择了，我用了半年多对它各方面都还相对满意。但是总不时会发现，Emacs 里面还有很多严重依赖\*Unix system 的操作。为了找到一劳永逸的办法（做梦），我尝试过 cygwin, mingw64 还做了一些功课，目前发现最好方式是，将整合到 msys2 里面使用，或者使用 msys2 编译的 Emacs。


## MSYS2 {#msys2}

---

MSYS2 是 MS-Windows 下编译自由/开源软件的一个环境，衍生自 Cygwin，也就是说它和 Cygwin 一样，编译出的程序不能脱离 Cygwin 环境运行(其实就是离不开那几个 DLL 文件)。但 MSYS2 有一个很牛的地方是它自带了 MinGW-w64，MinGW-w64 可以认为是 MinGW 的升级版本，编译出的程序是原生的 Windows 程序，最大的特点和名字一样，支持编译出 64 位的程序。目前 MSYS2 和 MinGW-w64 开发都很活跃，两者结合，既发挥了 MSYS2 对\*NIX 世界的兼容性，又能用 MinGW-w64 编译原生代码，很爽，自带的包很丰富，包管理采用 Arch Linux 用的 Pacman，非常的方便。

据说 msys2 目前是提供最多类 Unix 开发工具的环境，而且为想尝试\*unix 的 windows users 整体上提供了十分优秀的模拟环境。

Msys2 的下载安装都很简单，参照管网指南操作即可。中文用户配置可以参考以下链接：
<https://zhuanlan.zhihu.com/p/33751738>
<https://zhuanlan.zhihu.com/p/33789023>


## Compiling Emacs {#compiling-emacs}

---

_在 msys2 里面安装最简单的是使用 pacman -S Emacs，安装完的版本在 c:/msys2/usr/bin 里，dotfile 在 c:/msys2/home/user_.emacs.d 下方，我试图运行内置 function，正常，但是使用 dotfile 加载同样的 function 总显示加载错误。/

而且 chris 老师提到 Windows 下使用 emacs 最好的方式还是用自己编译的 Emacs，所以我也选择这么做。自编译 Emacs 要安装一系列 libraries，然后从原代码 git.sv.gnu.org/emacs.git 从这里 clone 所有的东西下来，按下列文章一步一步编译
<https://emacs-china.org/t/topic/3276/13>
<https://chriszheng.science/2015/03/19/Chinese-version-of-Emacs-building-guideline/>
<http://git.savannah.gnu.org/cgit/emacs.git/tree/nt/INSTALL.W64>

这个安装包都是为了在 msys2 中编译 Emacs 而写，所以安装途中不需要由什么特别改动的地方，注意一步一步执行代码就好。还有一点不得不提，Gti 自动改换行符的功能(autocrlf)很讨厌，会造成各种意想不到的神仙 bug（e.g. 很多人猜测这个也是造成 spacemacs 版本的 font-lock+ error 的原因）我们用下面的命令关掉它：
$ git config core.autocrlf false
`Update: 新版的git已经默认这项是关闭了。如果有需要，可以安装完后再把值改回true，一直默认关闭会导致有些git操作持续return warning，泪目。`

安装时需要一些依赖库，如果你的系统里面 MSYS2 已经被添加到 PATH 环境变量里(例如 PATH 里包含了 C:\msys2\mingw64\bin)，就不用从 mingwin64/bin 里面复制必用的 libraries 去 c:/emacs1/bin 了，所以直接在 PATH 里添加环境会比较方便。


## Advantages {#advantages}

---

跟以前使用的 Emacs for MS Win64 一样，emacs 配置文件还是默认在 C:/Users/AppData/Roaming/.emacs.d 中。大多数 package 放在本地 c:/msys2/home/user/.emacs.d/elpa/yourdir/以后使用 `(add-to-list 'exec-path "yourdir")` 即可正常调用。

但是我感觉从运行速度来讲，msys2 compiled Emacs 比 Emacs for Win64 `快很多` 。所以在 win 中使用 Emacs，虽然也有 WSL 或者 VM based 的解决方案，但是 msys2（在许多人看来）仍然是一个在 win 环境中使用类 unix 系统的优秀途径，希望未来能研究编译过的 emacs 在 msys2 提供的类 unix 系统里是否和其他 libraries 有更好的互动。
