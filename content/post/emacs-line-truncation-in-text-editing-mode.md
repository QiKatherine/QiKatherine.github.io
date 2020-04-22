+++
title = "Overview: Emacs line truncation in text editing 【Emacs org 换行/对齐/排版  汇总指南】"
summary = "Line visualization and navigation settings in Emacs text editing."
date = 2019-09-17T12:53:00+01:00
lastmod = 2020-04-22T03:27:21+01:00
tags = ["Emacs", "Org-mode"]
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

The text line (with line number) in Emacs, is called `logical line` . When a logical
line gets <span class="underline">too long</span> in typing window, Emacs provides two distinguish solutions: `line truncation` and `line wrapping` .


## Common approaches {#common-approaches}

---

Here is the link of technicalities of the solutions:

[Truncation - GNU Emacs Lisp Reference Manual - https://www.gnu.org/](https://www.gnu.org/software/emacs/manual/html%5Fnode/elisp/Truncation.hrequest json-mode all-the-icons-dired edit-indirecttml)

I draw a more straightforward figure:
![](/img/line operation 1.jpg)

• The breaked or wrapped line is refered as `screen line`, as opposed to
  `logical line`. Why do we care the
  differences? To precisely control the keybinding navigation between lines.

• Although the default line setting in Emacs is `wrapping on`, you may want to
  check your local setting with `C-h m` to see which exactly major/minor-mode
  you're using before rushing in trying other settings.

• If you're using `j(or k)` to navigate between lines, check which function it is binded
to. `evil-next-line` moves between <span class="underline">logical</span> lines. Conventionally this binds to `j` in
evil-mode and VIM. `evil-next-visual-line` moves between <span class="underline">screen</span> lines. Conventionally this binds to `gj`.

• Note most other line operation commands act on `logical lines`, NOT screen
lines. For instance, `C-k` kills a `logical line`.

• If making MS office word instances: `truncate off` and `text-mode` are like violent <span class="underline">justified on both sides</span>; `visual-line-mode`
  and `auto-fill-paragraph` are like <span class="underline">left alighned</span>.

• I personally use `auto-fill-paragraph` with self-setting fill-column to write
  articles, pressing `M-q` to arrange lines as I needed. It's neatly fast, coz other automated
  indentation rules are quite complicated and therefore slows your computer.

This may help you decide your configuration:
![](/img/line operation 2.jpg)


## More reading {#more-reading}

• This code can rearrange wrapped lines to long logical lines：

{{< highlight emacs-lisp >}}
;; unfill paragraph: the opposite of fill-paragraph
(defun y:unfill-paragraph-or-region (&optional region)
  "Takes a multi-line paragraph and makes it into a single line of text."
  (interactive (progn (barf-if-buffer-read-only) '(t)))
  (let ((fill-column (point-max))
        ;; This would override `fill-column' if it's an integer.
        (emacs-lisp-docstring-fill-column t))
    (fill-paragraph nil region)))
(define-key global-map "\M-Q" 'y:unfill-paragraph-or-region)
{{< /highlight >}}

• More helpful packages about line breaking：

[davidshepherd7/aggressive-fill-paragraph-mode: An emacs minor-mode for keeping paragraphs filled (in both comments and prose) - https://github.com/](https://github.com/davidshepherd7/aggressive-fill-paragraph-mode)
