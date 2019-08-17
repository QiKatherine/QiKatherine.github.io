+++
title = "Best workaround to use Emacs in MS Windows"
date = 2019-08-16T01:03:00+01:00
lastmod = 2019-08-16T01:03:32+01:00
draft = false
image = "/img/111.jpeg"
+++

因我我还是想建立自己的memo card系统，用了org-mode和ox-reveal把代码高亮，导出到ppt等一些问题解决了，做出了纸质的支持代码高亮的卡牌。

但是我又发现有一个开源的anki可以和org接起来，做成电子版，在手机上直接使用，所以我下载安装了anki, 学会使用，然后在emacs里安装anki-editor. 结果他依赖curl，装在cywin里面但是emacs并没有使用cywin里的，终于安装了mysy2，把所有的东西都放在一个类unix系统里。

按照官网直接下载，安装，配置参考以下链接
<https://zhuanlan.zhihu.com/p/33751738>
<https://zhuanlan.zhihu.com/p/33789023>

MSYS2简介
MSYS2是MS-Windows下编译自由/开源软件的一个环境，衍生自Cygwin，也就是说它和Cygwin一样，编译出的程序不能脱离Cygwin环境运行(其实就是离不开那几个DLL文件)。但MSYS2有一个很牛的地方是它自带了MinGW-w64，MinGW-w64可以认为是MinGW的升级版本，编译出的程序是原生的Windows程序，最大的特点和名字一样，支持编译出64位的程序。目前MSYS2和MinGW-w64开发都很活跃，两者结合，既发挥了MSYS2对\*NIX世界的兼容性，又能用MinGW-w64编译原生代码，很爽，自带的包很丰富，包管理采用Arch Linux用的Pacman，非常的方便。

在msys2里面安装最简单的是使用pacman -S Emacs，安装完的版本在c:/msys2/usr/bin里，dotfile在c:/msys2/home/user/.emacs.d下方，我试图运行内置function，正常，但是使用dotfile加载同样的function总显示加载错误。

而且chris老师提到Windows下使用emacs最好的方式还是用自己编译的Emacs，所以我也选择这么做。自编译Emacs要安装一系列libraries，然后从原代码
git.sv.gnu.org/emacs.git从这里clone所有的东西下来，按下列文章一步一步编译
<https://emacs-china.org/t/topic/3276/13>
<https://chriszheng.science/2015/03/19/Chinese-version-of-Emacs-building-guideline/>
<http://git.savannah.gnu.org/cgit/emacs.git/tree/nt/INSTALL.W64>

有一点不得不提，Gti自动改换行符的功能(autocrlf)很讨厌，下面的命令关掉它：
$ git config core.autocrlf false
很多人猜测这个也是造成spacemacs版本的font-lock+ error的原因，但是新版的git已经默认是关闭了。

安装时需要一些依赖库，如果你的系统里面MSYS2已经被添加到PATH环境变量里(例如PATH里包含了C:\msys2\mingw64\bin)，就不用从mingwin64/bin里面复制必用的libraries去c:/emacs1/bin了。

跟以前使用的Emacs for MS Win64一样，配置文件还是默认在c:/。。。roaming/.emacs.d种，但是运行速度新装的Emacs真的快很多。即便是在Windows中使用Emacs，也能发现有很多重度依赖类Unix的地方，虽然已经有WSL或者其它VM的解决方案，但是msys2仍然是一个在win环境中使用类unix系统给不错途径，希望未来能研究编译过的emacs在msys2提供的类unix系统里是否和其他libraries有更好的互动。
