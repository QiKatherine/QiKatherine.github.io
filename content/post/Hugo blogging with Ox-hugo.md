+++
title = "Hugo blogging with Ox-hugo"
publishDate = 2019-07-08T00:00:00+01:00
lastmod = 2019-08-14T20:50:31+01:00
tags = ["Hugo", "Ox-hugo"]
categories = ["TECH"]
image = "img/111.jpg"
draft = false
+++

我放弃Hexo，安装hugo的最初目的还是想用它配合Emacs-org-mode来写博客记笔记。灵感来自[子龙山人](https://zilongshanren.com/post/move-from-hexo-to-hugo/)和[贤民](https://www.xianmin.org/post/ox-hugo/)两位老师的博客，具体的安装和使用心得二位已经讲的比较详细，仔细读完会受益良多。本着不再重复造轮子的原则，我想简单写写hugo原生的设计,以及它和ox-hugo的对接原理与使用。

Hugo支持直接把.org文件渲染成html发布，但是许多人提到其实支持得不是很好。Hugo支持最好的markdown语法类型是blackfriday markdown。所以我们使用后端插件ox-hugo，它提供一种方法解决用 orgmode 写博文的问题：把org文件转成blackfriday markdown --- then use hugo server to generate html.

首先我们详细看ox-hugo官网对其功能的解说：
According to the information documentation, ox-hugo is an Org exporter backend that exports Org to Hugo-compatible Markdown (Blackfriday) and also generates the front matter (in TOML or YAML format).

简言之，我们主要使用ox-hugo做两件事（1）把org格式内容转换成markdown格式内容；（2）解析org file中的用org语法写front-matter，生成hugo语法的front-matter，进而使得生成的html能够正确被展示。那么front-matter具体指什么呢？


## Front matter {#front-matter}

Front matter give the information about the content, but NOT the information of content. It works as metadata to tell Hugo the general properties of the article. Hugo supports three types of front matter syntax: yaml, toml, json. Wheven you generate a new post/article/blog with

```
$ hugo new site posts
```

Hugo will automatically add front matter information at the top of the article like this:

```nil
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
```

所以想用org进行blog写作，也需要定义自己的front matter. 但是org file里front matter语法如下

```lisp
:PROPERTIES:
:EXPORT_FILE_NAME: ox-hugo-tutoria
:EXPORT_DESCRIPTION: Exporting to Hugo's Blackfriday Markdown from Orgmod
:EXPORT_HUGO_IMAGES: /img/org.pn
:END:
```

以:properties: 这块为代表的code block就是org以自己的方式定义metadata information。如前段提到，ox-hugo会解这个code block以生成hugo可以识别的YAML等front matter.

ox-hugo一般要求至少要有:EXPORT\_FILE\_NAME:，我们需要通过这个命令告诉ox-hugo"有新的标题和内容需要去export"。


## Don't get confused {#don-t-get-confused}

接下来这个问题可能对多大多数前端coder和Emacs熟练手都不是问题，但是这两个段头部代码被我着实混淆了一阵：
	Heading information指的是以以下语法结构为框架的代码#+hugo\_base\_dir:主管ox-hugo导出页面相关设置，例如谁是一级页面，二级页面，导出地址，导出栏目。

Front matter指的是以以下语法结构为框架的代码:PROPERTIES:主管面向一个article内部的性质设置，例如写作作者，写作日期，写作tag。

Heading information (#+hugo\_base\_dir)的概念局限于ox-hugo里；而front-matter在markdown，网页config file等其它文件里都有。只是:PROPERTIES:这种表达形式是org式写法。换做org支持的另一种projectile导出html的front matter可能是这样:base-directory "~/Dropbox/org/blog/"


## Content type {#content-type}

Content type 就是一系列不同的表达式样（layout），根据我们指定的不同的section type有不同表达式样法则，这里暂且把section翻译成一个网站下的不同栏目，例如blog，photo，quote，post，about，tages或者其它你想自定义的栏目。Hugo通过front-matter支持这些不尽相同的content type。

Hugo 认为每个栏目最好只做同一件事情，例如照片专栏只发发照片，post专栏集中发文章。所以除非我们自定义，hugo指定每个栏目的子单元都会自动继承一些此专栏pre-defined的特性，这样能最大限度的重复使用一个定义好的栏目，同时尽量减小‘config每个栏目’工作。

设定content type: 只需在源文件的头部引用hugo提供的heading information/metadata information（即front matter）即可，能迅速方便的修改一两个页面的layout。如果不能满足需求，可用hugo提供的自定义设置archetypes，按照hugo指定的结构组合方式，编写正确的\_index.md文件拼接好一个网站的layout即可。

如果你没有指定表达式样，比如暂时不太在乎如何展示photo这个栏目，Hugo有这么一个default设定：在front matter大部分信息缺乏的时候，通过每个文章存储path或者所在section猜出给这篇文章赋予什么layout。这会让我们在迅速上手写作blog的时候非常省心。


## Page boundles {#page-boundles}

Hugo 0.32以上的版本，使用page boundles的模式来管理网页源和图，从父子结构分类的角度看，有两种：leaf类页面和branch类页面。branch类页面允许在其内部嵌套更深层次的页面，而leaf规定其不能再有子页面。

任何一个叫index的页面文件都是leaf型，叫\_index的页面文件都是branch型。所以可见org文件里index的文件都会被输出成单页，没有子文件夹。最常见的index页面是下文会提到的分类里面的categories和tags index pages，它们都是单页，除此之外多数时候我们会使用branch型。如图:
<D:/Hugo/myblog/static/img/Hugo blogging with Ox-hugo 1.png >
Content文件夹在这里是home page, 他的主要功能是hosting“决定网站layout设定”的信息（在这里就是定义了branch型页面类型的\_index.md），所以hugo规定home page至多只能包含图片，而不能包含其它的content pages，只承担layout设定而不为article source提供场所。注意content里面的内容结构安排，应当和你想要渲染的网站结构一致。


## Section and nested section {#section-and-nested-section}

Section是一组页面的集合称呼，一般被放在content文件夹下面，就是上文提到的‘内容结构组织’的组成单元。从default设定来讲，content下面的每个一级文件夹自成一个root section。同时上面也提到section可以嵌套，即在一级文件夹下方再建二级section文件，构成一个更深层的section。

那么问题来了，hugo是如何知道nested section呢? 答案是：通过文件夹里要有\_index.md文件指定结构的设定。依此原理可以构建三级四级更深的section目录。 为了确保每一级网页都能被导览正确的链接到，每个最底层的文件夹里都要至少包含一个有内容文件，例如\_index.md.

```nil
content
└── blog        <-- Section, because first-level dir under content/
    ├── funny-cats
    │   ├── mypost.md
    │   └── kittens         <-- Section, because contains _index.md
    │       └── _index.md
    └── tech                <-- Section, because contains _index.md
        └── _index.md
```


## Head information {#head-information}

ox-hugo对org文件存放位置并没有特定要求，但是其头部的#+hugo\_base\_dir: 必须要被清晰的定义，因为这个地址告诉ox-hugo你的root directory在哪里，ox-hugo就会在这个地址下的content里面生成转化的md文件。很多用户自定义#+hugo\_base\_dir: ..即是本org文件所在的parent path.也有人定义#+hugo\_base\_dir: .代表path与现在的org文件同文件夹，如果root directory是跟现在org文件同文件夹，c-c c-c H A转化的结果就是这样：
![](/img/Hugo blogging with Ox-hugo 2.png)

仔细体会以下示例：以root目录c:\hugo\myblog\\为例：
(1) orgfile在myblog下方 且#+hugo\_base\_dir: .
(2) orgfile在myblog\content-org下方 且#+hugo\_base\_dir: ..
在c-c c-c H A 后都会产生如下形式，只不过(2)中hugotest.org在content-org里面
![](/img/Hugo blogging with Ox-hugo 3.png)


## Heading management {#heading-management}

The official documentation as well as the attached youtube tutorials have provided great explaintation of how hugo translate metadata of \_index.md files to the headings of html with Hugo heading management system.

建立一个有一篇文章的post
![](/img/Hugo blogging with Ox-hugo 4.png)

继续新增一个有两篇文章的fishsticks
![](/img/Hugo blogging with Ox-hugo 5.png)


## Tree and subtree writing {#tree-and-subtree-writing}

In normal Hugo, individual pages written in markdown (or now in org-mode) are placed inside the content directory inside the project root. With ox-hugo, a single org-mode file can be used to generate all pages, posts, and any other content. This has some advantages in allowing usage of org-mode functionality, as well as re-use of content or property settings across pages.

{{< figure src="/img/Hugo blogging with Ox-hugo 6.png" >}}


## Taxonomies 分类型页面 {#taxonomies-分类型页面}

• 这段是index管理page boundle的良好功能的又一个展现:通过 taxonomy index pages 就能建立一系列分类页面,例如tags and category,为分类页面单独建立管理page使拥有这些属性的文章被自右交叉引用,用户可以通过点击任何一个tag或者categories就能达到文章页面。在org写作里通过在headings添加实现，org到md转化由ox-hugo完成，语法差别很细微。如下图，还是上文的源码，只是为文章添加了两种categories，两种tag:
![](/img/Hugo blogging with Ox-hugo 7.png)

 在源码的三篇文章里分类update和reviews被提到两次，标签fear和herpes也被提到两次。从生成的html来看，
index.md刚好与之对应：分类的index page 提供了所有需要的分类（i.e. tags, categories）每个分类下还有list page显示所有与之相关的页面内容。导航就是这样实现建立的，使得我们能“实现不同分类间的交叉引用，点击任何一个入口进入文章”。

this is a test, idont know
