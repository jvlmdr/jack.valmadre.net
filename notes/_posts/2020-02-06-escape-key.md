---
layout: post
title:  Choosing an escape key for screen and tmux
date:   2020-02-06
math:   false
---

Usually I don't like to change the default terminal configuration because it becomes impossible to use someone else's computer or for them to use yours.
However, the default choices of `C-a` in screen and `C-b` in tmux are awful if you're used to emacs-style text navigation.
I have been using `C-]` for a few years and I'm pretty happy with it.
I wanted to document the reasoning behind this choice.

The criteria for an escape key are:
* it should not be a shortcut that you need to use often,
* it should not have dramatic consequences outside of screen and
* it should be easy to type (single modifier key).

Here is an evaluation of all 32 ASCII control characters in order.

Key | Code | Rating | Reason
----|------|--------|-------
`C-SPC` | 0 | no | emacs (set mark)
`C-a` | 1 | no | emacs (start of line)
`C-b` | 2 | no | emacs (move cursor back)
`C-c` | 3 | no | bash (sends SIGINT)
`C-d` | 4 | no | vim (page down)
`C-e` | 5 | no | emacs (end of line)
`C-f` | 6 | no | emacs (move cursor forward)
`C-g` | 7 | no | emacs (abort command)
`C-h` | 8 | no | backspace key; emacs (help prefix)
`C-i` | 9 | no | tab key `\t`; vim (move cursor to next position in history)
`C-j` | 10 | no | line feed `\n`; emacs (evaluate lisp expression)
`C-k` | 11 | no | emacs (kill to end of line)
`C-l` | 12 | no | bash (clear screen); emacs (scroll such that current line is at top/middle/bottom)
`C-m` | 13 | no | return `\r`
`C-n` | 14 | no | emacs (next line)
`C-o` | 15 | no | vim (move cursor to previous position in history)
`C-p` | 16 | no | emacs (previous line)
`C-q` | 17 | maybe | emacs (quoted insert; toggle read-only status)
`C-r` | 18 | no | vim (redo); shell (reverse search)
`C-s` | 19 | no | emacs (save current file)
`C-t` | 20 | maybe | emacs (transpose characters/lines)
`C-u` | 21 | no | bash (kill to start of line); vim (page up)
`C-v` | 22 | no | emacs (page down); vim (block-selection mode; insert control key)
`C-w` | 23 | no | emacs (kill region); bash (kill previous word); vimdiff (switch panes)
`C-x` | 24 | no | emacs (start of key sequence)
`C-y` | 25 | no | emacs (yank)
`C-z` | 26 | maybe | bash (sends SIGSTOP)
`C-[` | 27 | no | escape key (prohibitive for vim)
`C-\` | 28 | no | bash (sends SIGQUIT)
`C-]` | 29 | yes |
`C-^` | 30 | no | no easy way to type?
`C-/` | 31 | no | emacs (redo)
`C--` | 31 | no | equal to `C-/`
{: .table}

Note that the key combinations `C-;`, `C-'`, `C-.`, `C-,` and `C-=` do not correspond to control characters.

Just be careful; `C-]` can be right next to `C-\`, which sends SIGQUIT.
