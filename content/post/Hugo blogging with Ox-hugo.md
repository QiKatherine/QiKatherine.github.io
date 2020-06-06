+++
title = "Hugo blogging with Ox-hugo 【用 ox-hugo 在 Emacs 中搭建网站流】"
summary = "My personal experience of blogging with Emacs/Spacemacs and plug-in ox-hugo, along with some explanation of Hugo's working structure."
date = 2019-07-16T01:03:00+01:00
lastmod = 2020-06-06T02:36:55+01:00
tags = ["Hugo", "Ox-hugo"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

There have been many excellent articles talking about using `ox-hugo` to help with efficient blog-writing in Emacs/Spacemacs. I read these articles carefully several times and feel pretty confident using ox-hugo, so I would strongly recommend you give them a look:

![](/img/Hugo blogging with Ox-hugo 8.png) This is true for many cases, but no, not for Hugo or `ox-hugo`. Ox-hugo has fantastic official documentation! (Mr.Kaushal I know you occasionally search `ox-hugo` related articles in all different languages. If you happen to read this, thank you!)

• [ox-hugo official documentation](https://ox-hugo.scripter.co/) is very well written. The author carefully demonstrates not only the basics of using `ox-hugo`, but also some killer choices to improvise.

• [USING ORG MODE AND OX-HUGO TO REPLACE MARKDOWN IN HUGO WORKFLOW](https://gtpedrosa.github.io/blog/using-org-mode-and-ox-hugo-to-replace-markdown-in-hugo-workflow) and the three other articles mentioned are very helpful to me too.

• [Ken's ox-hugo tutorial](https://www.kengrimes.com/ox-hugo-tutorial/) is the source of some of my sections mentioned below, which you can see the snapshots are directly from Ken's blog. I re-edit it to help understand the logic of the section tree.

The content of this article comes from the Hugo official documentation along with the above articles. I am writing in Chinese because I noticed that there have not been many Chinese articles talking about Hugo and `ox-hugo`. If you are proficient in English reading, the official documentation and links above should be more than enough to get things done.

我放弃 Hexo，安装 Hugo 的最初目的，是因为 Hugo 的 Emacs 插件支持非常好。我的所有文本工作都用 Emacs 在管理，有了 Ox-hugo 这种插件就可以配合 Emacs-org-mode 来无缝衔接博客记笔记。配置灵感来自[子龙山人](https://zilongshanren.com/post/move-from-hexo-to-hugo/)和[贤民](https://www.xianmin.org/post/ox-hugo/)两位老师的博客，具体的安装和使用心得二位已经介绍的非常详细，仔细读完会受益良多。

本着不再重复造轮子的原则，这篇文章我想简单写写学习中遇到到有用的东西：Hugo 原生的结构设计；Hugo 与 `ox-hugo` 的对接原理；在 Emacs/Spacemacs 使用 Ox-hugo 帮助我们在 org-mode 以极高的效率写博客并发表。在阅读本文之前，强烈推荐阅读开头推荐的三部分内容。本篇文章主要就是整理了加工了一些来自 Hugo 官网，和以上三篇文章的内容，并配以更直观的图片帮助理解。

Emacs Org-mode 有一个优秀的原生功能是，可以用一个大文件加上多级别小标题，就能很方便的管理（包括编写，搜索，透视等等）一个超大型的文本文件树。对我来说
Ox-hugo 最牛的地方的在于, 它把 org-mode 原生的强大功集成到网站编写里来。也就是说，我们也可以通过一个文件管理整个网站的内容撰写。这是 Ox-hugo 这个插件的作者力荐的写作方式，也是他编写这个插件的主要初衷。比起各种单个文件组成的零散网站的文本管理模式，一个文件树的结构更清晰，管理更容易。作者用 `ox-hugo` 的官网直接展示了一个大型文件网站的样版：

[Why ox-hugo? — ox-hugo - Org to Hugo exporter - https://ox-hugo.scripter.co/](https://ox-hugo.scripter.co/doc/why-ox-hugo/)

这样的结构配合上一些 Emacs 自带的 killer 功能例如 `writeroom-mode`,
`org-tree-to-indent-buffer` , `predictive` 英文补全, `org-capture` 一键捕获，能瞬间让写作如虎添翼。


## 0. Org markup syntax and killer reference card {#0-dot-org-markup-syntax-and-killer-reference-card}

---

我一直都很难记住 org-mode 所有语法细节。官网给了零星几个常见的语法参考，但是 org-mode 能定制格式花样的远不止于此。所以当 org-mode 写作记不起语法的时候，我给出一个终极解决方式：参考这个网站原文的 org file。网站 org 源码从
org markup 形式到 hugo section 的 front information 都有涵盖，在网站看到想要的格式去原文直接搜索照搬即可。

Most efficient way to use org syntax is digging source samples from the official
website:

[Full website](https://ox-hugo.scripter.co/doc/hugo-section/)

[Full website source file](https://raw.githubusercontent.com/kaushalmodi/ox-hugo/master/doc/ox-hugo-manual.org)


## 1. Front matter 页首信息 {#1-dot-front-matter-页首信息}

---

Hugo 本身其实支持直接把.org 文件渲染成 HTML 发布，但是许多人提到其实支持得不是很好。Hugo 支持最好的 markdown 语法类型是 blackfriday markdown。对很多 Emacs user
来说 org-mode 就像把有力的大锤，碰上跟写作沾边的任务不能抡一下是很遗憾的。所以就有了这款非常棒的后端插件 `ox-hugo` 来支持 org-mode 写博文。它的解决方式是：把 org 文件转成 blackfriday markdown, 然后再生成 HTML 文件。首先我们详细看 `ox-hugo` 官网对其功能的解说：

> According to the information documentation, `ox-hugo` is an Org exporter backend that exports Org to Hugo-compatible Markdown (Blackfriday) and also generates the front matter (in TOML or YAML format).

简言之，我们主要使用 `ox-hugo` 做两件事（1）把 org 格式内容转换成 markdown 格式内容；（2）解析 org file 中的用 org 语法写 front-matter，生成 Hugo 要求语法的 front-matter，使得 Hugo 通过正确的信息生成的 HTML 。那么 front-matter 具体指什么呢？

Front matter give the information about the content, but NOT the information of content. It works as metadata to tell Hugo the general properties of the article. Hugo supports three types of front matter syntax: yaml, toml, json. Whenever you generate a new post/article/blog with

{{< highlight nil >}}
$ hugo new site posts
{{< /highlight >}}

Hugo will automatically add front matter information at the top of the article like this:

{{< highlight nil >}}
---
title: Good day
date: 2017-09-01T1705-43                    (YAML)
draft: true
---

+++
title= Good day
date= 2017-09-01T1705-43                   (TOML)
draft= true
+++

{
"title":  "Good day" ,
"date": "2017-09-01T1705-43",           (json)
"draft": "true"
}
{{< /highlight >}}

所以想用 org 进行写作，也需要定义自己的 front matter. 但是 org 语法里 front
matter 定义如下

{{< highlight lisp >}}
:PROPERTIES:
:EXPORT_FILE_NAME: ox-hugo-tutorial
:EXPORT_DESCRIPTION: Exporting to Hugo's Blackfriday Markdown from Orgmode
:EXPORT_HUGO_IMAGES: /img/org.pn
:END:
{{< /highlight >}}

以 `:properties:` 这块为代表的代码就是 org 以自己的方式定义 meta
information。=(ox-hugo)= 会解析改写这个这些代码以生成 hugo 可以识别的 YAML 等 front matter.
Ox-hugo 一般要求至少要有 `:EXPORT_FILE_NAME:` 。我们需要通过这个命令告诉
ox-hugo “有新的标题和内容需要去导出”。


## 2. Don't get confused 易混淆的概念 {#2-dot-don-t-get-confused-易混淆的概念}

接下来这个问题可能对多大多数前端 coder 和 Emacs 熟练手都不是问题，但是这两个段头部代码被我着实混淆了一阵：

通用 Front matter 主管面向一个 article 内部的性质设置，例如写作作者，写作日期，写作 tag。Heading information 例如 `#+hugo_base_dir` 的概念局限于 `ox-hugo` 里，是遵从
org-mode 特色的命名方式设计的变量，类似的语法在其他 org 文章的管理信息中也可以看到。

而 front-matter 这些变量在 markdown，网页配置等其它文件里都有。只是 `:PROPERTIES:` 这种表达形式是 ox-hugo 特色写法。换做 org 支持的另一种 projectile 导出 HTML 的 front matter
可能是这样: `（base-directory "~/Dropbox/org/blog/")`.


## 3. Content type {#3-dot-content-type}

---

Content type 就是一系列不同的表达式样（layout），根据我们指定的不同的 section type 有不同表达式样法则，这里暂且把 section 翻译成一个网站下的不同栏目，例如 blog，photo，quote，post，about，tages 或者其它你想自定义的栏目。Hugo 通过 front-matter 支持这些不尽相同的 content type。

Hugo 认为每个栏目最好只做同一件事情，例如照片专栏只发发照片，post 专栏集中发文章。所以除非我们自定义，hugo 指定每个栏目的子单元都会自动继承一些此专栏 pre-defined 的特性，这样能最大限度的重复使用一个定义好的栏目，同时尽量减小‘config 每个栏目’工作。

设定 content type: 只需在源文件的头部引用 hugo 提供的 front matter 即可，能迅速方便的修改一两个页面的版式。如果不能满足需求，可用 hugo 提供的自定义设置 archetypes，按照 hugo 指定的结构组合方式，编写正确的\_index.md 文件拼接好一个网站的
layout 即可。

如果你没有指定表达式样，比如暂时不太在乎如何展示 photo 这个栏目，Hugo 有一个默认设定：在 front matter 大部分信息缺乏的时候，通过每个文章存储
path 或者所在 section 猜出给这篇文章赋予什么 layout。这会让我们在迅速上手写作 blog 的时候非常省心。


## 4. Page bundles {#4-dot-page-bundles}

---

Hugo 0.32 以上的版本，使用 page bundles 的模式来管理网页源和图，从父子结构分类的角度看，有两种：leaf 类页面和 branch 类页面。branch 类页面允许在其内部嵌套更深层次的页面，而 leaf 规定其不能再有子页面。

任何一个叫 index 的页面文件都是 leaf 型，叫\_index 的页面文件都是 branch 型。所以可见 org 文件里 index 的文件都会被输出成单页，没有子文件夹。最常见的 index 页面是下文会提到的分类里面的 categories 和 tags index pages，它们都是单页，除此之外多数时候我们会使用 branch 型。如图:
</img/Hugo blogging with Ox-hugo 1.png >
Content 文件夹在这里是 home page, 他的主要功能是 hosting“决定网站 layout 设定”的信息（在这里就是定义了 branch 型页面类型的\_index.md），所以 hugo 规定 home page 至多只能包含图片，而不能包含其它的 content pages，只承担 layout 设定而不为 article source 提供场所。注意 content 里面的内容结构安排，应当和你想要渲染的网站结构一致。


## 5. Section and nested section {#5-dot-section-and-nested-section}

---

Section 是一组页面的合集称呼，一般被放在 content 文件夹下面，就是上文提到的‘内容结构组织’的组成单元。从 default 设定来讲，content 下面的每个一级文件夹自成一个 root section。同时上面也提到 section 可以嵌套，即在一级文件夹下方再建二级 section 文件，构成一个更深层的 section。

那么问题来了，hugo 是如何知道 nested section 呢? 答案是：通过文件夹里要有\_index.md 文件指定结构的设定。依此原理可以构建三级四级更深的 section 目录。 为了确保每一级网页都能被导览正确的链接到，每个最底层的文件夹里都要至少包含一个有内容文件，例如\_index.md.

{{< highlight nil >}}
content
└── blog        <-- Section, because first-level dir under content/
    ├── funny-cats
    │   ├── mypost.md
    │   └── kittens         <-- Section, because contains _index.md
    │       └── _index.md
    └── tech                <-- Section, because contains _index.md
        └── _index.md
{{< /highlight >}}


## 6. Head information {#6-dot-head-information}

---

`ox-hugo` 对 org 文件存放位置并没有特定要求，但是其头部的 `#+hugo_base_dir:` 必须要被清晰的定义，因为这个地址告诉 `ox-hugo` 你的 root directory 在哪里，
`ox-hugo` 就会在这个地址下的 content 里面生成转化的 md 文件。很多用户自定义
`#+hugo_base_dir:` ..即是本 org 文件所在的 parent path.也有人定义
`#+hugo_base_dir:` .代表 path 与现在的 org 文件同文件夹，如果 root directory 是跟现在 org 文件同文件夹，c-c c-e H A 导出 markdown 文件的结果就是这样：
![](/img/Hugo blogging with Ox-hugo 2.png)

仔细体会以下示例：以 root 目录 c:\hugo\myblog\\为例：
(1) orgfile 在 myblog 下方 且#+hugo\_base\_dir: .
(2) orgfile 在 myblog\content-org 下方 且#+hugo\_base\_dir: ..
在 c-c c-c H A 后都会产生如下形式，只不过(2)中 hugotest.org 在 content-org 里面
![](/img/Hugo blogging with Ox-hugo 3.png)


## 7. Heading management {#7-dot-heading-management}

---

The official documentation as well as the attached youtube tutorials have
provided great explaintation of how hugo translate metadata of \_index.md files
to the headings of HTML with Hugo heading management system.

建立一个有一篇文章的 post
![](/img/Hugo blogging with Ox-hugo 4.png)

继续新增一个有两篇文章的 fishsticks
![](/img/Hugo blogging with Ox-hugo 5.png)


## 8. Tree and subtree writing {#8-dot-tree-and-subtree-writing}

---

In normal Hugo, individual pages written in markdown (or now in org-mode)
	are placed inside the content directory inside the project root. With `ox-hugo`, a single org-mode file can be used to generate all pages, posts, and any other content. This has some advantages in allowing usage of org-mode functionality, as well as re-use of content or property settings across pages.

{{< figure src="/img/Hugo blogging with Ox-hugo 6.png" >}}


## 9. Taxonomies 分类型页面 {#9-dot-taxonomies-分类型页面}

---

这段是 index 管理 page boundle 的良好功能的又一个展现:通过 taxonomy index pages 就能建立一系列分类页面,例如 tags and category,为分类页面单独建立管理 page 使拥有这些属性的文章被自右交叉引用,用户可以通过点击任何一个 tag 或者 categories 就能达到文章页面。在 org 写作里通过在 headings 添加实现，org 到 md 转化由 `ox-hugo` 完成，语法差别很细微。如下图，还是上文的源码，只是为文章添加了两种 categories，两种 tag:
![](/img/Hugo blogging with Ox-hugo 7.png)

在源码的三篇文章里分类 update 和 reviews 被提到两次，标签 fear 和 herpes 也被提到两次。从生成的 HTML 来看，
index.md 刚好与之对应：分类的 index page 提供了所有需要的分类（i.e. tags, categories）每个分类下还有 list page 显示所有与之相关的页面内容。导航就是这样实现建立的，使得我们能“实现不同分类间的交叉引用，点击任何一个入口进入文章”。
