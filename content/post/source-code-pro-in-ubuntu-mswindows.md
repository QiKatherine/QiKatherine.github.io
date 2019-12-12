+++
title = "Installing Source Code Pro in Ubuntu and MS Windows platform 2019 【2019 版 Source Code Pro 字体安装指南】"
summary = "Installing source code pro and trouble shooting."
date = 2019-09-03T21:53:00+01:00
lastmod = 2019-12-12T01:13:01+00:00
tags = ["Ubuntu"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

使用 Emacs 的时候，有时候会用到 Source Code Pro 字体，尤其是 Spacemacs 以它作为默认字体。未安装会造成 Emacs 启动时出现报错。可以使用以下方式安装 2019 年 2.03
版本字体。


## MS Windows {#ms-windows}

以下网址给了详细的图片操作步骤：
[Simple Tutorials - htddtps://simpletutorials.com/](https://simpletutorials.com/c/2759/How+to+install+the+default+Spacemacs+font+on+Windows)


## Ubuntu {#ubuntu}

Linux 下安装，由下载，解压，编译，粘贴，删除源文件等一系列操作组成，所以我附上 shell 脚本一键操作。脚本来自：[rogerpence.com | Install Source Code Pro font on Ubuntu - https://www.rogerpence.com/](https://www.rogerpence.com/posts/install-source-code-pro-font-on-ubuntu)

【注意】如果手动输入，或者代码报错，文件名称最好使用 `自动补全` 。

1.  Home 目录下新建脚本

<!--listend-->

{{< highlight emacs-lisp >}}
touch ~/install-source-code-pro.sh
{{< /highlight >}}

1.  把脚本模式改成可执行文件

<!--listend-->

{{< highlight elisp >}}
sudo chmod +x install-source-code-pro.sh
{{< /highlight >}}

1.  填写脚本内容并保存

<!--listend-->

{{< highlight sh >}}
#!/usr/bin/env bash
cd Downloads

wget https://github.com/adobe-fonts/source-code-pro/archive/2.030R-ro/1.050R-it.zip

if [ ! -d "~/.fonts" ] ; then
mkdir ~/.fonts
fi

unzip 1.050R-it.zip

cp source-code-pro-*-it/OTF/*.otf ~/.fonts/
rm -rf source-code-pro*
rm 1.050R-it.zip

cd ~/

fc-cache -f -v
{{< /highlight >}}

1.  执行脚本

<!--listend-->

{{< highlight shell >}}
./install-source-code-pro.sh
{{< /highlight >}}

使用愉快:)
