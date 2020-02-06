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

Key | Rating | Reason
----|--------|-------
`C-SPC` | no | emacs (set mark)
`C-a` | no | emacs (start of line)
`C-b` | no | emacs (move cursor back)
`C-c` | no | bash (sends SIGINT)
`C-d` | no | vim (page down)
`C-e` | no | emacs (end of line)
`C-f` | no | emacs (move cursor forward)
`C-g` | no | emacs (abort command)
`C-h` | maybe | emacs (help prefix)
`C-i` | no | vim (move cursor to next position in history)
`C-j` | maybe | emacs (evaluate lisp expression); line feed
`C-k` | no | emacs (kill to end of line)
`C-l` | no | bash (clear screen); emacs (scroll such that current line is at top/middle/bottom)
`C-m` | maybe | return character
`C-n` | no | emacs (next line)
`C-o` | no | vim (move cursor to previous position in history)
`C-p` | no | emacs (previous line)
`C-q` | maybe | emacs (quoted insert; toggle read-only status)
`C-r` | no | vim (redo); shell (reverse search)
`C-s` | no | emacs (save current file)
`C-t` | maybe | emacs (transpose characters/lines)
`C-u` | no | bash (kill to start of line); vim (page up)
`C-v` | no | emacs (page down); vim (block-selection mode; insert control key)
`C-w` | no | emacs (kill region); bash (kill previous word); vimdiff (switch panes)
`C-x` | no | emacs (start of key sequence)
`C-y` | no | emacs (yank)
`C-z` | maybe | bash (pause process and send to background)
`C-[` | no | equal to `ESC` (prohibitive for vim)
`C-\` | no | bash (sends SIGKILL)
`C-]` | yes | can be dangerously near to `C-\`
`C-^` | no | no easy way to type?
`C-/` | no | emacs (redo)
`C--` | no | equal to `C-/`

Note that the key combinations `C-;`, `C-'`, `C-.`, `C-,` and `C-=` do not correspond to control characters.
