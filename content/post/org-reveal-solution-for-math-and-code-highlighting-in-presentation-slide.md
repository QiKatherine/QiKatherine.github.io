+++
title = "Org-reveal: solution for math and code highlighting in presentation slide 【在 ppt 中展示代码高亮，数学公式的优秀解决方案】"
summary = "Learning notes and trouble shooting for using org-reveal."
date = 2019-08-23T22:50:00+01:00
lastmod = 2020-01-04T01:45:03+00:00
tags = ["Emacs", "Org-mode"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

I have used flash card for remembering new things for years. Before using software like org-drill or Anki, I was pretty much putting everything in slides, printing on papers and cutting it into a portable sized card and carried in my pocket. So I have been exploring an ultimate solution of perfect formatting for everything. This picture shows what I feel about slides making tools.
![](/img/org-reveal.jpg)

I always thought math functions display tricky, but the Latex with Beamer has provided an adequately good template for most people. As a comparison, the code highlighting is trickier, especially for not-so-prevalent programming languages like Lisp. In order to adequately demonstrate code highlighting, sometimes people have to paste code in Notepad++ with designated formatting, then paste into MS word, then to MS PowerPoint. Or take an alternative hustle to explore various online highlighting transformation tool. If you are looking for a long term hustles solution, then I think "Emacs/Spacemacs + Org-mode + org-reveal" makes an excellent tool for you.
• [yjwen/org-reveal: Exports Org-mode contents to Reveal.js HTML presentation. - https://github.com/](https://github.com/yjwen/org-reveal/)
• [How to create slides with Emacs Org mode and Reveal.js | Opensource.com](https://opensource.com/article/18/2/how-create-slides-emacs-org-mode-and-revealjs)
• [reveal.js – The HTML Presentation Framework - https://revealjs.com/](https://revealjs.com/?transition=fade#/)

The above links give many details of the code/manual/demo of org-reveal.Specifically, the second and third links provide excellent instruction about how to toggle and customize your presentation. I highly recommend you to give them a look.

In this article, I am only adding a few trouble shootings for the issue that I met.

The installation did three things (1)installing ox-reveal (2)installing reveal.js (3)installing htmlize, but the spacemacs comes with htmlize installed.

I add ox-reveal in the package list of spacemacs dotfile, reloading the dotfile but it did not installed. The author also mentioned that ox-reveal in MELPA maybe out of date. So alternatively, I downloaded the .el file and manually required it.

There are also two ways of calling reveal.js as described by the readme. I am using the second where the source url was put in the config file. Notice there seems to be an old url(<http://cdn.jsdelivr.net/reveal.js/3.0.0/>) which does NOT work any more. If your exported HTML file is just an empty page with theme background, check if you are referring to the right url. The current source and config code is shown below:

{{< highlight emacs-lisp >}}
;; Emacs
(require 'ox-reveal)
(setq Org-Reveal-root "file:///path-to-reveal.js")
(setq Org-Reveal-title-slide nil)


;; Spacemacs/Using use-package
(defun yourname/post-init-ox-reveal ()
  (use-package ox-reveal
    :ensure t
  (setq org-enable-github-support t)
  (setq org-enable-reveal-js-support t)
  (setq org-reveal-root "https://cdn.jsdelivr.net/npm/reveal.js")))
{{< /highlight >}}
