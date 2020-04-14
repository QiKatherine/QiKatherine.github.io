+++
title = "Building personal website with Hugo"
date = 2020-04-13T22:57:00+01:00
lastmod = 2020-04-14T17:36:21+01:00
draft = false
image = "img/111.jpg"
+++

This is a minimum todo list for one to use a static website generator to build a
personal website. The are a number of them such as Jekyll, Hexo, Octopress, World press,
Hugo, etc. I have tried all of the above and personally prefer Hugo for speed and
flexibility and compatibility with my Emacs.

If you are interested in building a personal wiki, academic profile, home
cooking recipe album, small business official website, building and managing a
website by these types of generator facilitates you with most flexibility with the
minimum cost.

By most flexibility, I mean you can change the look of your website any time
with a number of fantastic designs, and upload articles/photo whenever you want.
Check out the hundreds of themes Hugo:
[Complete List | Hugo Themes](https://themes.gohugo.io/)

By minimum expenditure, I mean zero cost, unless you want to get a customized
website name like _awesome.ai_ or rent some cloud based services to boost up
your website.

To do:
The process can be roughly showed in this pic:
![](/img/hugo.png)


## 1. Register a GitHub account and install local terminal. {#1-dot-register-a-github-account-and-install-local-terminal-dot}

[An Intro to Git and GitHub for Beginners (Tutorial)](https://product.hubspot.com/blog/git-and-github-tutorial-for-beginners)

This is where you host your website for others to visit with the minimum cost. Purchasing personal server/VPS, using Gitlab or other cloud based service has its own benefit, but requires some more work or expenditure.

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

To rewrite the great theme template to your website, all you need to do is modifying
the template information in **config.toml** file. For example, section two of this
article gives an instruction of changing config.toml in theme casper [[<https://brent-li.github.io/post/build-personal-site-with-hugo/>][]<https://brent-li.github.io/post/build-personal-site-with-hugchange themes>.


## 4. Adding articles and local test {#4-dot-adding-articles-and-local-test}

By the end of the third step, you should have a nice empty website in your name
with your photo. More you can do is adding articles. The article are all saved
in post folder, where you could initiate with:

{{< highlight shell >}}
cd myblog
hugo new post/name-of-new-article.md
{{< /highlight >}}


## 5. Pushing work to remote server. {#5-dot-pushing-work-to-remote-server-dot}


## 6. More to do {#6-dot-more-to-do}

So far the minimum work that makes a sustainable website is done. Of course
there are more work to escalate the efficiency and fun.
