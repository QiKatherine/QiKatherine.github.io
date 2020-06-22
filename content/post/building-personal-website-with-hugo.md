+++
title = "Building personal website with Hugo in one hour"
date = 2020-04-13T22:57:00+01:00
lastmod = 2020-06-22T01:23:19+01:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

This is a minimum todo list for one to use a static website generator to build a
personal website. The are a number of them such as Jekyll, Hexo, Octopress, World press,
Hugo, etc. I have tried all of the above and personally prefer Hugo for speed and
flexibility and compatibility with my Emacs. But another reason I recommend Hugo
to everyone is that: the Hugo community is really well-developed and supportive.
This is very important for non-proficient users to get help and not be baffled
by technicalities, so that we can get things done.

If you are interested in building a personal wiki, academic profile, home
cooking recipe album, small business official website, building and managing a
website by these types of generator facilitates you with most flexibility with the
minimum cost.

By most flexibility, I mean you can change the look of your website any time
with lots and lots of fantastic designs, and upload articles/photo whenever you want.
Check out the hundreds of themes Hugo:
[Complete List | Hugo Themes](https://themes.gohugo.io/)

By minimum expenditure, I mean zero cost, unless you want to get a customized
website name like _awesome.ai_ or rent some cloud based services to boost
visitors experience with your website.

To do:
The process can be roughly showed in this pic:
![](/img/hugo.png)


## 1. Register a GitHub account and install local terminal. {#1-dot-register-a-github-account-and-install-local-terminal-dot}

[An Intro to Git and GitHub for Beginners (Tutorial)](https://product.hubspot.com/blog/git-and-github-tutorial-for-beginners)

Normally people use GitHub to share and maintain code repositories with
collaboration. But it can also host website repository for other people to visit. Using GitHub to
host personal website is free and encouraged.

There are other options, such as purchasing personal server/VPS, using Gitlab or
other cloud based service. Each of them has its own advantage, but requires some more work or expenditure.

Installing local terminal to push your local edited website content to remote
server. For example, if you are using Windows, install git bash from: [Git for Windows](https://gitforwindows.org/)

_You don't have to learn all the git command unless you want to contribute to software/theme development. Building website do not refer those information. Only minimum
experience with command is sufficient._


## 2. Download Hugo executable and installment. {#2-dot-download-hugo-executable-and-installment-dot}

Everything you need to know is here, for all types of operating systems:
[Install Hugo | Hugo](https://gohugo.io/getting-started/installing/)

If you are a proficient command line user of Linux/macOS, `(homebrew)` it!

If you are a windows user and not sure which one to follow, the video in above
link should be sufficient. The download link he talks about is in **Less-technical Users** section. But make sure you update the environment variable. This
is where your git bash could find the Hugo binary and access to it.
[Create and Modify Environment Variables on Windows](https://docs.oracle.com/en/database/oracle/r-enterprise/1.5.1/oread/creating-and-modifying-environment-variables-on-windows.html#GUID-DD6F9982-60D5-48F6-8270-A27EC53807D0)


## 3. Download and implement a Hugo theme. {#3-dot-download-and-implement-a-hugo-theme-dot}

You could take your time browse the Hugo theme shop and choose a theme according
to your needs. There are demos, which are real websites that people build with the
corresponding theme. Don't just look the front page, make sure you like the
structure, navigation style and everything since there are so many options!

You can change easily later if you want different things every now and then.

Step by Step instructions to download and implement a theme:
[Installing & Using Themes | Hugo - Static Site Generator | Tutorial 5 - YouTube](https://www.youtube.com/watch?v=L34JL%5F3Jkyc)
Notice that he is using a macOS, if you are using Windows, the command line tool
he is using, is the Git bash in your environment.

If you are happy with the way domo site looks like, the easiest way is just to copy `(content, static,
config.toml)` from `themes/your-theme/(exampleSite)` and paste them with replacement to
home file. To rewrite the great theme template to your website, all you need to do is modifying
the template information in **config.toml** file.

Example of themes:
Casper [用Hugo搭建个人网站 · 李子峰的Github Page](https://brent-li.github.io/post/build-personal-site-with-hugo/)

LeaveIt <https://mogeko.me/2018/018/>


## 4. Adding articles and local test {#4-dot-adding-articles-and-local-test}

By the end of the third step, you should have a nice empty website in your name
with your photo. More you can do is adding articles. The article are all saved
in post folder, where you could initiate with:

{{< highlight nil >}}
cd myblog
hugo new post/name-of-new-article.md
{{< /highlight >}}

You can always preview it in local host:

{{< highlight nil >}}
hugo server themename
{{< /highlight >}}

Open web browser input `(http://localhost:1313)` to preview. Input `(ctrl-c)` in
Git bash to cease preview.


## 5. Pushing work to remote server. {#5-dot-pushing-work-to-remote-server-dot}

Make sure you generate html files into /public file first:

{{< highlight nil >}}
hugo -t themename
{{< /highlight >}}

Push everything generate in /public file to `(XX.github.io)` repository.

{{< highlight nil >}}
git init
git add .
git commit -m "Initial commit."
git remote add origin git@github.com:Brent-Li/brent-li.github.io.git
git push -u origin master
{{< /highlight >}}

Unless you want to giving version control, not only for your website, but also for the
source code. Then you are gonna have to do some work because there are many ways
to achieve. I personally use two branches (dev for source code, and master for
/public) to manage because I prefer to keep codes intact with non-bloat management.


## 6. More to do {#6-dot-more-to-do}

So far the minimum work that makes a sustainable website is done. Of course
there are more work to escalate the efficiency and fun. Such as:

(1). Get a personalized website domain (address) for people to remember.
This is one of the places where could use some money. GitHub has specific
requirement for using `(XX.github.io)` as name for website, which is hard to
remember, so you might want to check for buying a website domain with your own name:
[Buy domain name - Cheap domain names from $1.37 - Namecheap](https://www.namecheap.com/)
(There are many domain name sellers, all secondary distributors, so there are
slightly price different for a same domain.)

At here you need to link the domain you bought to GitHub, so that when people
are visiting `(xx.com)`, GitHub will re-direct to `(xx.github.io)`.
[How do I link my domain to GitHub Pages - Domains - Namecheap.com](https://www.namecheap.com/support/knowledgebase/article.aspx/9645/2208/how-do-i-link-my-domain-to-github-pages)

Chinese instruction:
[NameCheap域名解析教程 —— BasicDNS - 知乎](https://zhuanlan.zhihu.com/p/33261777)

(2). Get Google Analytics to get statistics of the visitors of your website.
[Internal Templates | Hugo](https://gohugo.io/templates/internal/)
The process only needs adding two lines of codes. But this is a controversial demand because some people care about privacy or refuse
to create by traffic driven.

Chinese instruction:
[Hugo中使用Google Analytics · 零壹軒·笔记](https://note.qidong.name/2017/07/05/google-analytics-in-hugo/)

(3). Adding Disqus for visitor to comments

The website we generate is static website, which means generally the interactive
operations like searching/chatting/commenting is not supported. But there are
plug-in can help achieve.

(4). Adding site search with Algolia

(5). Auto deployment

There are many automated deployment tools online, I use Werecker and I wrote an
article about the details:
[Hugo Blogging with Wercker Auto Build & Deployment 【用 Wercker 自动部署网站】-Katherine He Blog|何琪的博客](http://localhost:1313/post/hugo-blogging-with-wercker-auto-build-deployment/)

(6). Search Engine Optimization

(7). Redevelopment of website theme

(8). Emacs users with ox-hugo

I have written an article about this in another post:
[Hugo blogging with Ox-hugo 【用 ox-hugo 在 Emacs 中搭建网站流】-Katherine He Blog|何琪的博客](https://sheishe.xyz/post/hugo-blogging-with-ox-hugo/)
