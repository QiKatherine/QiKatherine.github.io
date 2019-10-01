+++
title = "Using Anki-editor to build flashcard in Org-mode 【用 Anki-editor 在 org-mode 中生成复杂记忆卡片】"
date = 2019-09-30T23:54:00+01:00
lastmod = 2019-10-01T23:51:24+01:00
tags = ["windows", "org-mode", "Anki"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

## 1. Anki {#1-dot-anki}

---

One of a major activity in my daily work is to learn new things. I have rely
heavily on paper flashcard which gives me most flexibility on keeping tables, code, math
functions etc. But the shortcoming is pretty obvious: there is no `scientific
reviewing cycle`, and it's getting cumbersome to maintain and carry with.

Then I dived into different softwares and discovered Anki has been exceled in
every aspect that I could ever ask for. Let's see how Anki describe itself:

> Anki is a program which makes remembering things easy. Because it's a lot more efficient than traditional study methods, you can either greatly decrease your time spent studying, or greatly increase the amount you learn.
>
> Anyone who needs to remember things in their daily life can benefit from Anki. Since it is content-agnostic and supports images, audio, videos and scientific markup (via LaTeX), the possibilities are endless.
> For example:
>
> • Learning a language
> • Studying for medical and law exams
> • Memorizing people's names and faces
> • Brushing up on geography
> • Mastering long poems
> • Even practicing guitar chords!

However, importing complex content
requires certain hacking of HTML. So the trick is: using org-mode with Emacs
the org HTML export backend will export it to HTML. The Anki adds-on
`AnkiConnect` will generate the ontent to flashcard.

[Anki - powerful, intelligent flashcards - https://apps.ankiweb.net/](https://apps.ankiweb.net/)


## 2. Installation {#2-dot-installation}

---

The full installation link can be found from github repo:
[louietan/anki-editor: Emacs minor mode for making Anki cards with Org - https://github.com/](https://github.com/louietan/anki-editor)

• Installing `AnkiConnect` in **Anki**: Tools -- adds-on -- Get Add-ons with
code: 2055492159.
![](/img/anki.png)

• Installing `curl` in **MS Windows**:

There are several ways of applying `curl` in windows: build-in curl, curl in
Msys2, and scoop installed curl. The former two methods always have connecting issues
in Emacs, so my way is to: `delete Msys2 curl and install with scoop.`

• Installing `Anki-editor` in **Emacs**::

-   Vanilla Emacs:

<!--listend-->

{{< highlight nil >}}
(use-package anki-editor
  :ensure t)
{{< /highlight >}}

-   Spacemacs:

<!--listend-->

{{< highlight nil >}}
dotspacemacs-additional-packages '(anki-editor)
{{< /highlight >}}


## 3. Usage {#3-dot-usage}

---

First of all, making sure Anki is `running all the time` so that Emacs can connect
with it. `M-x anki-editor-mode` to enble minor-mode.
![](/img/anki2.png)
Note:
`Error communicating with AnkiConnect using cURL: exited abnormally with code 2`
means Emacs has trouble finding curl. Check if the path of curl has been added in exec-path.

`Error communicating with AnkiConnect using cURL: exited abnormally with
code 7`
means you need to run `Anki` before running Emacs command.
