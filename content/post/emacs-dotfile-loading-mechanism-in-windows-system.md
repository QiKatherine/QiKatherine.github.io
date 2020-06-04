+++
title = "Emacs dotfile loading mechanism in Windows system"
date = 2020-05-11T02:29:00+01:00
lastmod = 2020-06-04T02:46:09+01:00
draft = false
image = "img/111.jpg"
+++

This post is about `(default directory)`.

As one may notice that calling Emacs from different places might not always be the same. The default dotfile in Windows nowadays is at `("c:/Users/YOU/AppData/Roaming/.spacemacs.d/")` Clicking runemacs.exe or calling from Command line Prompt, it can automatically load dotfile (e.g. theme, spacemacs). But calling from Msys or Mingw git bash, it loads vanilla Emacs. Although when you run `(echo %HOMEPATH%)` in Powershell Prompt, or run `(echo $HOME)` in git bash, they all return the same HOME as `("c:/Users/YOU")`.

Why is git bash loaded Emacs not recognizing its dotfile? (In another words why can powershell prompt find dotfile not saved at HOME?)

This is because Emacs for Windows has its own setting coping the `(default-directory)`. When being called by git bash, Emacs inherits `(default-directory)` from git bash of `("c:/Users/YOU")`. But when being called from runemacs.exe and you didn't specify default-directory (by right-click icon - properties - start-in), Emacs didn't have place to inherit so it uses build-in default-directory, which is `("c:/Users/YOU/AppData/Roaming/")`. This can be verified by launching Emacs from different places and calling `(c-h v)`.

This is upsetting especially for Spacemacs who specially creates `(.env)` file to read the HOME variable from bash, which happens to stop Emacs loading dotfile in Windows since most users put dotfile in Roaming file, not in HOME of bash.

This is also the reason that calling Emacs from different places and launching `(c-x c-f)`, the directories are different. Of course at the same time, you can always reset the default working directory in dotfile as in answer from here: [customization - Changing the default folder in Emacs - Stack Overflow](https://stackoverflow.com/questions/60464/changing-the-default-folder-in-emacs).
