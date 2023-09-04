+++
title = "Switching back from Emacs to Neovim"
date = 2023-03-13
+++

I started off as a vim cultist and used it for practically everything. At the
time I was primarily doing C development on my own small codebases. However,
after needing to do development on larger typescript codebases for work, my
needs got to be more complex. The biggest thing missing for me was a LSP. For
this reason, I started using neovim as my IDE and gradually shifted over for
all software related tasks. I still occasionally used vim for simple text
editing.

At a certain point, I was just bored and decided to give emacs a shot. It has
a lot of great defaults and feels pleasant to use after getting evil mode and
my own vim bindings configured. I also really enjoyed some of the emacs
plugins, especially [org-tree-slide](https://github.com/takaxp/org-tree-slide),
it made giving presentations awesome and I used it for my rust workshop.

However, I did not like a lot about my emacs experience:
- Performance: felt very sluggish compared to vim/nvim
- LSPs constant crashing: when working with rust projects, rust analyzer would constantly crash and ask to be restarted, making for a painful experience
- Buffer switching keeps showing random buffers I did not open myself like \*scratch\*, \*straight-process\* and a billion others, tried a bunch of stuff to fix this
- Clipboard pasting is slow and blocks
- Emacs takes a while to start up
- Haven't found nice way to reload `init.el` when I make a change, makes prototyping/experimenting with options more difficult
- The last straw was when emacs started terminating for no reason whatsoever

In general, my todo list of emacs improvements involves fixes with the editor
rather than further advancements and features I could be adding to my workflow.
In the end, it feels more like I was fighting the editor, this may just be
perhaps that I come from vim and expect emacs to behave more like vim.

Overall I'm really impressed by a lot of the things emacs is capable of but for
now I just want a fast and productive IDE to write software with. I'm too lazy
to keep fighting emacs - although I'm sure it's a great editor if I learned how
to use it properly. I'll come back one day...
