+++
title = "Mastering emacs in 21 days learning notes - 1 【21 天学会 Emacs 笔记 - 1】"
summary = "Learning notes about Emacs."
date = 2019-08-25T23:51:00+01:00
lastmod = 2020-01-05T23:56:50+00:00
tags = ["Emacs"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

This article is part of my learning notes of Mastering Emacs in 21 Day, which is
a series of Chinese based tutorials post by [zilongshanren (子龙山人) -
https://github.com/](https://github.com/zilongshanren) The official learning note is at here: [Master Emacs in 21
Days - http://book.emacs-china.org/](http://book.emacs-china.org/) My notes extend the official notes with my
personal learning experience. Since there has been ample discussion of using and
learning Emacs in English community, my learning note is written in Chinese to
benefit more additional readers.

这篇文章是我学习子龙山人老师的 spacemacs rock 系列笔记之一。在原视频配套的基础上我还做了一些扩展和补充，有的知识点还加了视频对应【集数-分钟】的时间点，以便迅速观看视频.


## 1. 基本知识 {#1-dot-基本知识}

---

• Emacs 相当于一个 elisp based 的操作系统。这个操作系统的原理是，每次 Emacs 启动过程就相当于一系列功能通过 loading files(代码块)的实现。在每次使用前，成百上千的
functions 被加载到 workspace 中(其中一些带着 default 参数) ，等待被调用，或者被
custermize。因此所有的设置，架构都可以通过调 function portal 修改成想要的 value；或者在原有的 value/function 的基础上，继续开发一系列指令来增进，比如我们自己编写的各种自定义函数。连整个 emacs 的启动都可以概括为一句话：加载一系列脚本。只不过这些脚本有的是内置的（built in），有的来自安装的插件包，有的是我们自己写的。配置
emacs 归根结底是在配置各种各样的脚本。

• 首次加载一个配置复杂/成熟的 Emacs（例如 spacemacs 或 Purcell 的 Emacs），会耗费比较长的时间，因为需要依次安装所有 cofig.el 中提到过的 packages。在经过首次配置之后的时间里，每次启动 Emacs 的 loading file 主要以加载和更新为主，而极少数
package 安装只有才加载检查发现没有 package 时候才会发生。

• loading 的文件主要是.elc 文件，是经过编译的.el 文件的二进制形式，加载更快。但平日的修改是在更容易阅读的.el 文件上进行的，所以如果你手动修改完.el 文件，一定要记得编译以便 Emacs 自动执行，For example with Emacs-Lisp you do:

{{< highlight emacs-lisp >}}
(byte-compile-file "foo.el")
{{< /highlight >}}

否则 Emacs 要么加载没有被同步修改的二进制.elc 文件，要么会因为没找到.elc，去加载更缓慢的.el 文件。

• 光标放在最后一个反括号的末尾，按 C-x C-e，是执行一行命令 on the fly，作用等同于 M-x 命令 回车。


## 2. 新建 init.el【26’50】 {#2-dot-新建-init-dot-el-26-50}

---

• 初始 hacking：
Emacs 像一个状态机，即使还没 config init.el, 裸机 Emacs 也加载了许多 build-in
functions 以确保能被基本使用。所有的状态在 default value 下运行。在这种情况下，可以通过 M-x 调用已有的命令来做到修改设置，但是所有临时设置的东西关掉后都会被删除，还原成默认值，被称为 `临时改动` 。还有一种就是直接去 el/.elc 的脚本里修改代码 hard coding modify，有很多坏处。比如，每次更新插件，都要自己回去重新修改，被称为 `永久改动` 。

• 初始化设置：所以更好的选择是不动原脚本，通过预加载修改达到目的，也就是手动写一份 init.el 的意义。为了使得 emacs 每次打开都有最佳设置，我们在 C:\Users\AppData\Roaming\\.emacs.d\\文件下新建了 init.el 的 elisp 文件，来写想要的配置。因为 Emacs 默认设置打开时，会自动寻找 home 目录的.emacs.d\\文件下下面 init.el 文件来执行：（1）如果找得到，每次开启 Emacs 都先重新执行一遍我们的 config，以达到预加载我想要的全部舒适配置；（2）如果其不存在 init.el，Emacs 还是原始裸机也能用；（3）如果 init.el 代码有错没加载成，也是裸机（后面使用 usepackage 来管理初始加载，可以避免这种“因为一点小错误”使得整个初始加载都失败”的问题）。
	`注意：** 如果希望把配置放在 ~/.emacs.d/init.el 文件中，那么需要手工删除 ~/.emacs 文件。`

• 使用 init.el 管理 personalized config 额外的好处是，init.el 文件还可以在
GitHub 备份，在初始化文件里加上一个系统类型判断函数，让我们在任何地方的的不同主流系统都可以自由使用。甚至，不用修改别人电脑里有的 Emacs 配置，用 U 盘就能在一个 Emacs 里使用不同的 config。

• Emacs 的命令执行是按顺序来的，这个顺序既只文件也只内部命令。各种 function 一个一个的被调用 （也就是 load/require），一行完成后再进行下一行。例如，只保存第 1 个命令，下次打开 Emacs 显示字体为 16pt；保存 1.2 命令，在 1 之上 load open-init-file 命令去 workspace；保存 1.2.3 命令，在 12 之上还能使得我们通过按 f2 真正调用这个 open-init-file:

{{< highlight emacs-lisp >}}
;; 更改显示字体大小 16pt
(set-face-attribute 'default nil :height 160)                   ---- 1

;; 快速打开配置文件
(defun open-init-file()
  (interactive)
  (find-file "~/.emacs.d/init.el"))                             ---- 2

;; 这一行代码，将函数 open-init-file 绑定到 <f2> 键上
(global-set-key (kbd "<f2>") 'open-init-file)                   ---- 3
{{< /highlight >}}

这个知识点目前看起来很简单，但是以后涉及到要去其它.el 文件层层加载，记得这个顺序性 load 的特质会帮助理解 Emacs 的加载机制。

• 在 Emacs 里命令按行顺序执行 A--C，如果遇到“call A 的前提是先要加载 B function”（但是 B 没有加载在 workspace 里的情况时），Emacs 会先走开，去 B.el 相关的文件 load B function，执行完再回来继续加载剩余的东西，然后再执行 C。因此相互依赖的 feature 有可能因为调用顺序没安排好而导致 initiliaze 出错，这样能解决。为了解决依赖顺序造成的潜在问题，Purcell 写了一个 after-load 函数，目的是把一些相互依赖的 feature 的加载顺序理顺，例如 feature A 依赖于 feature B，则可以写成(after-load 'B 'A)，这样如果错误地在 B 之前 require 了 A 也不会影响正常启动：

{{< highlight emacs-lisp >}}
(defmacro after-load (feature &rest body)
  "After FEATURE is loaded, evaluate BODY."
  (declare (indent defun))
  `(eval-after-load ,feature
     '(progn ,@body)))
{{< /highlight >}}


## 3. Major mode and minor mode {#3-dot-major-mode-and-minor-mode}

---

• 在开始配置之前让我们先来区别 Emacs 中 major mode 与 minor mode 的区别。Major mode 通常是定义对于一种文件类型编辑的核心规则，例如语法高亮、缩进、快捷键绑定等。 而 minor mode 是除去 major mode 所提供的核心功能以外的额外编辑功能（辅助功能）。 例如在下面的配置文件中 _tool-bar-mode_ 与 _linum-mode_ 等均为 minor mode。

【查看 minor mode】简单来说就是，一种文件类型同时只能存在一种 major mode 但是它可以同时激活一种或多种 minor mode。鼠标放在 powerline 可以显示一些 minor mode 信息，如果你希望知道当前的模式全部信息，可以使用 `C-h m` 来显示当前所有开启 的全部 minor mode 的信息。（你如果发现已经设置过的 mode 没开，可能因为没有设置成 global 的）。

• major mode 里面还有一个重要的概念是 hook。一个 major mode（ _e.g.
Emac-lisp-mode_ ）相当于一个 list，就是一些它自带的 function。但这里还可以有一串儿 minor mode 挂在上面。这个 major mode 开启默认所有 list 上的特性都会被自动加载。如果我们需要的设置没有，需要手动添加，有可能是通过 hook，一般对于每个特定的 pack
如果使用 hook，GitHub 上有具体设置指南。例如 `(add-hook 'emacs-lisp-mode-hook
'show-paren-mode)` .
![](/img/emacs 21 1-1.jpg)

• Hook 就是一串特定的 functions: A hook is a Lisp variable which holds a list of functions, to be called on some well-defined occasion. 大部分 hook 都尽量是 normal 且一致的，方便全局调用，我们也会自己通过 add-hook 加 function 到 hook 上来满足特殊的需求。自行设计 hook list 要注意顺序问题，因为上文提到一串 function 是按顺序依次执行的，如果后面的会影响前面的，那么顺序自定义就很重要。相关阅读: [Hooks - GNU Emacs Manual - https://www.gnu.org/](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Hooks.hrequest json-mode all-the-icons-dired edit-indirecttml)

• Emacs 操作系统很像一个大的状态机，储存着很多可修改的状态。Mode 调用和设置也是通过 function 修改 value 实现。Emacs 虽然因为没有变量空间而导致所有变量全局可见,但是因为 mode 的 default 设置，使得有些 value 只是 buffer local 的(aka mode 每个 buffer 都独立保留了一份 default 值)，如果需要在全局应用某些 mode，要注意上 hook 或者修改 global setting，注意查看每个安装文档的说明。

• 如上文所说，让 mode 生效有三种方式（1）临时调用 M-x company-mode，可以反复修改 value，但有可能只修改了临时 buffer local value（2）直接修改 mode.el 脚本；都不如这种好：(3) 写好 mode 设置放在 init.el 里面让它在 Emacs 开启时设置好。【2-2'10】以 company-mode 为例讲解以上知识：mode 的种类（还有其他 state）开启还是关闭，本身是 value，每个 buffer 都有储存一份，所以 setq 只会修改本 buffer 的值，
setq-default 才会修改全体 buffer 的值。只有当一个 value 生来就是全局变动的时候，
setq 和 setq-default 才是一回事。set-key 也是类似，如下注意左右列的区别，尤其当想要的修改下次没生效，查看变量是否是 buffer local 很重要。例如以下区别：

| Local setting           | Global setting                   |
|-------------------------|----------------------------------|
| (company-mode t)        | (global-company-mode t)          |
| (setq cursor-type 'bar) | (setq-default  cursor-type 'bar) |
| (set-key ..)            | (global-set-key …)               |


## 4. 在 init.el 中安装 packages {#4-dot-在-init-dot-el-中安装-packages}

---

• 裸机 Emacs 系统除了部分内置的功能，什么 customized 设置都没有，因此我们手动安装想要 packages。第一次安装是从 option-manage packages 用 GUI 安装，等同于调用 M-x package-list-packages，但安装不仅是加载，系统同时自动同时在 init.el 生成 M-x package-list-packages list，以便以后在任何电脑上都可以自动复现。所以我们可以从 init.el 从命令的角度看看这是如何实现的。

• 以后我们也会通过在 init.el 里编写 packages list 来实现群体安装。

• 默认 packages 都装在.emacs.d/elpa 目录下面，即所有有关这个 package 的文件都下载到一个文件夹下面，以供 emacs load【注意这个跟.emacs.d/lisp 文件不要混淆】。


### 4.1 Auto-load 【2-15’00】 {#4-dot-1-auto-load-2-15-00}

• 装好后重新打开 Emacs，我们看到 init.el 文件第一行要求是
`(package-initialize)` 意思是自动去 elpa 目录里找安装好的 package，挨个扫描，找到 package-autoload.el 文件执行，预加载一些函数名进 workspace。为什么会有再初始时就有加载 autoload 这一过程呢？

• 请思考如下问题。如果没有 autoload，你可以在 init.el 加载时就 load 各种各样的脚本，使得 emacs 在启动时就把整个使用过程中可能用到的函数一次性准备好。但这样真的好么？
autoload 告诉 emacs 某个地方有一个定义好的函数，并且告诉 emacs，先别加载，只要记住在调用这个函数时去哪里寻找它的定义即可。这样做的一个好处是，避免在启动 emacs 时因为执行过多代码而效率低下，比如启动慢，卡系统等。想象一下，如果你安装了大量的有关 python 开发的插件，而某次打开 emacs 只是希望写点日记，你肯定不希望这些插件在启动时就被加载，让你白白等上几秒，也不希望这些插件在你做文本编辑时抢占系统资源（内存，CPU 时间等）。所以，一个合理的配置应该是，当你打开某个 python 脚本，或者手动进入 python 的编辑模式时，才加载那些插件.

• autoload 定义的函数都可以直接调用，而不需要 require，like company-mode。所以 autoload 行为的意义用一个简单的概括是：“只注册函数名而不定义函数本身”。

它执行过程如下，以 company 为例。在这个 package 安装好后 ，我们可以在.emacs.d/elpa 下看到 company 文件夹，包含了 company-xxxfunction.el 和一系列自解码.elc 二进制文件，这些即是 company-mode 的全部执行细节。Emacs 会自动遍历 company-20160325 里面所有文件，提取所有注释里有魔法语句；；;autoload 的内容，并根据这个注释自动生成一个一个的魔法语句块，全部存在 company-autoload.elc 文件里。例如一下魔法语句块就是根据第一行从 company.el 自动生成的：


### 4.2 Non-autoload[ ] {#4-dot-2-non-autoload}
