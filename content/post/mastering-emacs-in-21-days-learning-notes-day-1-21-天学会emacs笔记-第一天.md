+++
title = "Mastering emacs in 21 days learning notes | 21 天学会Emacs笔记"
date = 2019-08-25T23:51:00+01:00
lastmod = 2019-08-28T23:56:10+01:00
tags = ["Emacs"]
categories = ["TECH"]
draft = false
+++

This article is part of my learning notes of Mastering Emacs in 21 Day, which is a series of Chinese based tutorials post by [zilongshanren (子龙山人) - https://github.com/](https://github.com/zilongshanren) The official learning note is at here: [Master Emacs in 21 Days - http://book.emacs-china.org/](http://book.emacs-china.org/)  My notes extend the official notes with my personal learning experience. Since there has been ample discussion of using and learning Emacs in English community, my learning note is written in Chinese to benefit more addtional readers.

这篇文章是我学习子龙山人老师的spacemacs rock系列笔记之一。在原视频配套的基础上我还做了一些扩展和补充，有的知识点还加了视频对应的时间点，希望能帮到一些人:).


## 1. 基本知识 {#1-dot-基本知识}

• 【系统】Emacs相当于一个elisp language based的操作系统。这个操作系统的原理是，每次Emacs启动过程就相当于一系列功能通过loading files(aka代码块)的实现。在每次使用前，成百上千的functions被加载到workspace中with default setting，等待调用，或者被custermize。因此所有的设置，架构都可以通过调function portal修改成想要的value；或者在原有的value/function的基础上，继续开发一系列指令来增进，比如我们自己编写的各种自定义函数。连整个emacs的启动都可以概括为一句话：加载一系列脚本。只不过这些脚本有的是内置的（built in），有的来自安装的插件包，有的是我们自己写的。配置emacs归根结底是在配置各种各样的脚本。

• 首次加载一个配置复杂/成熟的Emacs（例如spacemacs或Purcell的Emacs），会耗费比较长的时间，因为需要依次安装所有cofig.el中提到过的packages。在经过首次配置之后的时间里，每次启动Emacs的loading file主要以加载和更新为主，而极少数package 安装只有才加载检查发现没有package时候才会发生。

• loading的文件主要是.elc文件，是经过编译的.el文件的二进制形式，加载更快。但平日的修改是在更容易阅读的.el文件上进行的，所以如果你手动修改完.el文件，一定要记得编译以便Emacs自动执行，For example with Emacs-Lisp you do:

```nil
(byte-compile-file "foo.el")
```

否则Emacs要么加载没有被同步修改的二进制.elc文件，要么会因为没找到.elc，去加载更缓慢的.el文件。

• 光标放在最后一个反括号的末尾，按C-x C-e，是执行一行命令on the fly，作用等同于M-x 命令 回车。


## 2. 新建init.el【26’50】 {#2-dot-新建init-dot-el-26-50}

• 【init.el】初始hacking：
Emacs像一个状态机，即使还没config init.el, 裸机Emacs也加载了许多build-in functions以确保能被基本使用。所有的状态在default value下运行。在这种情况下，可以通过M-x调用已有的命令来做到修改设置，但是所有临时设置的东西关掉后都会erase，还原成默认value--【临时改动】。还有一种就是直接去el/.elc的脚本里修改代码hard coding modify，有很多坏处。比如，每次更新插件，都要自己回去重新修改--【永久改动】。

所以更好的选择是不动原脚本，通过预加载修改达到目的，也就是手动写一份init.el的意义。为了使得emacs每次打开都有最佳设置，我们在C:\Users\heqi2\AppData\Roaming\\.emacs.d\\文件下新建了init.el的elisp文件，来写想要的配置。因为Emacs默认设置打开时，会自动寻找home目录的.emacs.d\\文件下下面init.el文件来执行：（1）如果找得到，每次开启Emacs都先重新执行一遍我们的config，以达到预加载我想要的全部舒适配置；（2）如果其不存在init.el，Emacs还是原始裸机也能用；（3）如果init.el代码有错没加载成，也是裸机（后面使用usepackage来管理初始加载，可以避免这种“因为一点小错误”使得整个初始加载都失败”的问题）。
	`注意：** 如果希望把配置放在 ~/.emacs.d/init.el 文件中，那么需要手工删除 ~/.emacs 文件。`

• 使用init.el管理personalized config额外的好处是，init.el文件还可以在GitHub备份，换电脑也可以用，甚至不用修改别人电脑里有的Emacs配置，用U盘就能在一个Emacs里使用不同的config。

• Emacs的命令执行是按顺序来的，这个顺序既只文件也只内部命令。各种function一个一个的被call（也就是load/require），一行完成后再进行下一行。例如，只保存第1个命令，下次打开Emacs显示字体为16pt；保存1.2命令，在1之上load open-init-file命令去workspace；保存1.2.3命令，在12之上还能使得我们通过按f2真正调用这个open-init-file:

```nil
;; 更改显示字体大小 16pt
(set-face-attribute 'default nil :height 160)                   ---- 1

;; 快速打开配置文件
(defun open-init-file()
  (interactive)
  (find-file "~/.emacs.d/init.el"))                             ---- 2

;; 这一行代码，将函数 open-init-file 绑定到 <f2> 键上
(global-set-key (kbd "<f2>") 'open-init-file)                   ---- 3
```

这个知识点目前看起来很简单，但是以后涉及到要去其它.el文件层层加载，记得这个顺序性load的特质会帮助理解Emacs的加载机制。

•在Emacs里命令按行顺序执行A--C，如果遇到“call A的前提是先要加载B function”（但是B没有加载在workspace里的情况时），Emacs会先走开，去B.el相关的文件load B function，执行完再回来继续加载剩余的东西，然后再执行C。因此相互依赖的feature有可能因为调用顺序没安排好而导致initiliaze出错，这样能解决。为了解决依赖顺序造成的潜在问题，Purcell写了一个after-load函数，目的是把一些相互依赖的feature的加载顺序理顺，例如feature A依赖于feature B，则可以写成(after-load 'B 'A)，这样如果错误地在B之前require了A也不会影响正常启动：

```nil
(defmacro after-load (feature &rest body)
  "After FEATURE is loaded, evaluate BODY."
  (declare (indent defun))
  `(eval-after-load ,feature
     '(progn ,@body)))
```


## Major mode and minor mode {#major-mode-and-minor-mode}

• 【mode基础】在开始配置之前让我们先来区别 Emacs 中major mode 与 minor mode 的区别。Major mode 通常是定义对于一种文件类型编辑的核心规则，例如语法高亮、缩进、快捷键绑定等。 而 minor mode 是除去 major mode 所提供的核心功能以外的额外编辑功能（辅助功能）。 例如在下面的配置文件中 _tool-bar-mode_ 与 _linum-mode_ 等均为 minor mode。

【查看minor mode】简单来说就是，一种文件类型同时只能存在一种 major mode 但是它可以同时激活一种或多种minor mode。鼠标放在powerline可以显示一些minor mode信息，如果你希望知道当前的模式全部信息，可以使用 `C-h m` 来显示当前所有开启 的全部minor mode的信息。（你如果发现已经设置过的mode没开，可能因为没有设置成global的）。

• 【hook】major mode里面还有一个重要的概念是hook。一个major mode（ _e.g. Emac-lisp-mode_ ）相当于一个list，就是一些它自带的function。但这里还可以有一串儿minor mode挂在上面。这个major mode开启默认所有list上的特性都会被自动加载。如果我们需要的设置没有，需要手动添加，有可能是通过hook，一般对于每个特定的package如果使用hook，GitHub上有具体设置指南。例如 `(add-hook  'emacs-lisp-mode-hook  'show-paren-mode)` .
![](/img/emacs 21 1-1.jpg)

• Hook 就是一串特定的functions: A hook is a Lisp variable which holds a list of functions, to be called on some well-defined occasion. 大部分hook都尽量是normal且一致的，方便全局调用，我们也会自己通过add-hook加function到hook上来满足特殊的需求。自行设计hook list要注意顺序问题，因为上文提到一串function是按顺序依次执行的，如果后面的会影响前面的，那么顺序自定义就很重要。相关阅读: [Hooks - GNU Emacs Manual - https://www.gnu.org/](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Hooks.html)

• Emacs操作系统很像一个大的状态机，储存着很多可修改的状态。Mode调用和设置也是通过function修改value实现。Emacs虽然因为没有变量空间而导致所有变量全局可见，但是因为mode的default设置，使得有些value只是buffer local的(aka mode每个buffer都独立保留了一份default 值)，如果需要在全局应用某些mode，要注意上hook或者修改global setting，注意查看每个安装文档的说明。

• 如上文所说，让mode生效有三种方式（1）临时调用M-x company-mode，可以反复修改value，但有可能只修改了临时buffer local value（2）直接修改mode.el脚本；都不如这种好：(3) 写好mode设置放在init.el里面让它在Emacs开启时设置好。

【mode和setq】第二课【2'10】：以company-mode为例讲解以上知识：mode的种类（还有其他state）开启还是关闭，本身是value，每个buffer都有储存一份，所以setq只会修改本buffer的值，setq-default才会修改全体buffer的值。只有当一个value生来就是全局变动的时候，setq和setq-default才是一回事。set-key也是类似，如下注意左右列的区别，尤其当想要的修改下次没生效，查看变量是否是buffer local很重要。例如以下区别：

| (company-mode t)        | (global-company-mode t)          |
|-------------------------|----------------------------------|
| (setq cursor-type 'bar) | (setq-default  cursor-type 'bar) |
| (set-key ..)            | (global-set-key …)               |
