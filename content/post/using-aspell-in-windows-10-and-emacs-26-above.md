+++
title = "Using aspell in windows 10 and Emacs 26 above 【拼写检查 Emacs26 使用更新版 aspell】"
summary = "Installing aspell for Emacs 26+ in windows system."
date = 2019-09-13T01:34:00+01:00
lastmod = 2019-12-24T01:04:17+00:00
tags = ["Emacs", "Spacemacs", "Windows10"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

I just realized that my ispell doesn't work after updating my Emacs to 27.0
version. I kept getting errors that:

{{< highlight emacs-lisp >}}
aspell release 0.60 or greater is required
{{< /highlight >}}

[flyspell - aspell with emacs 26.1 on ms windows - Emacs Stack Exchange - https://emacs.stackexchange.com/](https://emacs.stackexchange.com/questions/41892/aspell-with-emacs-26-1-on-ms-windows/45752#45752)

The above discussion shows that by the time being of emacs 26 released, there was no
binary aspell in windows OS, so the workaround was to use hunspell. Now the
`final solution` has been provided by `installing aspell with *Msys2*`.

更新 Emacs 以后发现 aspell 不能用了，调用 `ispell-minor-mode` 时一直收到以上报错，查看以上 stack exchange 的答案发现， 错误原因是 Emacs 26 以上的版本刚发布的时候 windows 还没有与之匹配的 aspell 安装版本，所以当时解决方式时暂时用 hunspell 代替。现在，匹配版 aspell 已经发布, 所以下文记录了： `用msys2安装aspell` 。

1.  In MingW64 terminal search aspell:

    {{< highlight nil >}}
    pacman -Ss aspell
    {{< /highlight >}}

2.  Installing `aspell` and `dictionary you need` :

    {{< highlight nil >}}
    pacman -S mingw64/mingw-w64-x86_64-aspell
    pacman -S mingw64/mingw-w64-x86_64-aspell-en
    {{< /highlight >}}

3.  Find aspell.exe location with `which aspell`, e.g. `C:\msys64\mingw64\bin`

4.  Update in dotfile. Especially in Spacemacs:

    {{< highlight emacs-lisp >}}
    (add-to-list 'exec-path "C:/msys64/mingw64/bin/")
    (setq ispell-program-name "aspell")
    (setq ispell-personal-dictionary "c:/msys64/mingw64/lib/aspell-0.60/en_GB")
    {{< /highlight >}}

Done.

---

More awesome (Chinese) articles of spell checking in Emacs, reading with google
translation if needed:

[Emacs帮你进行英文写作 - 暗无天日 - http://blog.lujun9972.win/](http://blog.lujun9972.win/blog/2018/06/03/emacs帮你进行英文写作/)

[ispell与emacs的拼写检查 | HaHack - https://www.hahack.com/](https://www.hahack.com/tools/ispell-and-flyspell/)
