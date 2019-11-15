+++
title = "Migrating habituated cut copy and pasting into Emacs"
date = 2019-11-14T11:40:00+00:00
lastmod = 2019-11-15T16:13:01+00:00
tags = ["Emacs", "Spacemacs"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

In order to get as versitile as possible, I try to avoid re-config keybindings
in different operating environment. I made myself to get used to the
environment, for example there are about four different settings in Emacs, VIM,
bash, Windows OS and other terminal. I find this brings me more benefit than
the cost of learning or migrating customerized config to other environment.

In modern system we fix text work with two steps: (1) copy new content (2) mark the
old content and directly paste to replace. But `the old systems do not have
replace function` and this makes the above procedure 3 steps: (1) copy new
content (2)mark the old content and delete (3) paste new content. However the `killing` function is
actually `cutting`: the deleted stuff overwrites the content you were about to
paste in clipboard. (Of course you could press many backspaces in insert-mode to keep the
killing ring/clipboard right but no one really do that) So I found my actual
operation often became more steps (1) I copied new content (2) I marked places
and cut it (3) paste and remembered the clipboard has been overwrote. (4) I went back and re-copied new content (5) I pasted it and got totally
pissed off.

Wether making killing function as cutting is a good design or not, at this point
the best solution is in my opinion is keeping multiple history in clipboard.
Using `helm-show-kill-ring` to keep 5 recent deleted records for your further
concern. This not only frees your mind of remembering the cut/copy operation,
but also save your time for repetitive work.
