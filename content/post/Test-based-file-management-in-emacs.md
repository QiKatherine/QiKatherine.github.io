+++
title = "Text based file management in Emacs 【Emacs 文本文件管理】"
summary = "Overview of text process tools in org-mode."
date = 2019-09-22T23:56:00+01:00
lastmod = 2019-09-30T00:37:33+01:00
draft = false
image = "img/111.jpg"
+++

People discuss the most efficient way text management methods for a number of reasons: someone wants to build a digitalized notebooks for new
knowledge; Someone wants to manage the increasing case files in office work;
Someone wants to review thousands of articles they hoard on the internet. Either
way, I call that 'text digesting system' and buckle up, I got MANY opinions on
this topic.

I think we have all been struggling in choosing tools: some excel at
supporting markdown, some are good at coding highlighting, some support
real-time online collaboration or even text searching in images. If one has not used Emacs, I would probably
recommend Evernote or Onenote. But if you're an Emacs user, this is the chance of
tasting the one-for-all solution.

Emacs org-mode meets different needs with:

• How to take note: speech input in org-mode

• How to memorize knowledge: Anki + Anki-editor

• How to classify: categories and tags

• How to search: helm-ag + regex

• How to visualize structure: Knowledge Graph; Daft; NotDeft; etc.

• How to present in slides: Org-reveal

• How to generate static website: Hugo + Ox-hugo


## 1. Text search and classification {#1-dot-text-search-and-classification}

---

Softwares like Evernote or Onenote is excellent in most daily work. However, they also
have glitches when there are a thousand files to manage, which adding together became the reason that I
moved my text work to Emacs. For example, the most common way to manage files
is using categories and tags (or tweaked as pages/binders/etc.) The limitation
of using category is that you can only allocate an article ONCE (what about articles
inherently belong to two or more categories?). The tags seem to
help address this issue, and yet I noticed it's still not enough in practical
work. My opinion is that: `the classification of an article should be better decided by the *whole article*,
rather than several keywords.`
After a long while of managing hundred notes, I noticed the most frequently used (and
efficient) function is =global search =.  I still use categories and tags,but
that's just to maintain my overview of the structure of all articles.

I use `helm-ag`, the silver search which is a searcher reconstructed with C and
it's SO FAST, especially in large files or codes with over 400,000 lines.
The linux command line search tool speed ranks as: `ag > pt > ack > grep`.
Acorrding to requent users, the `ag` search is 5-10 times faster than `ack` on average.

This is my spacemacs config file, which is a big text file tree that I
need to comb through constantly.
![](/img/searching2.png)

`M-x helm-ag ("path-to-file")` enbles text search. Without path parameter, it
searches all files under parent file of current buffer. For example,
searching `zilongshanren` in `~/.spacemacs.d/init.el` buffer.
![](/img/searching3.png)


## 2. Structure visualization {#2-dot-structure-visualization}

---

Org-mode seems to encourage or intentionally facilitate you organize text
articles in ONE file. For example, all this website is written in one file with
different categories to distinguish taxonomy. All the org-capture facilitated
items such as to-do or blog idea is managed in a single file.

In this case there are lots of software you can use to illustrate the in-file
structure.

Check this:
[Intro to Mindmap, Gantt Chart, Graphviz - http://ergoemacs.org/](http://ergoemacs.org/misc/mindmap%5Fgantt%5Fgraphviz.html)


## 3. Knowledge graph {#3-dot-knowledge-graph}

---

This is for cases where you want lots of cross-references on files may be in
different directories:

[Semantic Synchrony, ultrafast video demo - YouTube - https://www.youtube.com/](https://www.youtube.com/watch?v=R2vX2oZmUUM&feature=youtu.be)
[
A video introduction to Semantic Synchrony · synchrony/smsn Wiki -
https://github.com/](https://github.com/synchrony/smsn/wiki/A-video-introduction-to-Semantic-Synchrony)


## 4. Anki {#4-dot-anki}

---

<span class = "underline">baby</span>

@@html:<span style="color:red"> TODO</span>
