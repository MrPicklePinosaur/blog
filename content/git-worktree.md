+++
title = "PSA: so git worktree exists..."
date = 2024-04-23
+++

Recently for work related things I find myself having to juggle development and
testing on across multiple branches at any given time. To make matters worse,
we are running an absolutely humongous monorepo to the scale where it takes
like a solid _minute_ to switch branches. This quickly got annoying when I just
needed to hop over to some branch and reference some code or make a small
change. Or maybe I would like to be doing development on some branch but have
another editor open and reference code from another branch.

I didn't think something like this was possible without cloning the repo again
under a different path, so I went searching to see if some chad developer had
already created some open source tool for this. However it turns out the git
team had already thought about this and implemented
[**git-worktree**](https://git-scm.com/docs/git-worktree). Absolute lads 👏.

With **git-worktree**, you have the ability to checkout multiple working trees
that are attached to the same repo. Personally, I create a `.worktree`
directory in my home (arguably not the best choice 😛) and put all of my
worktrees there. An alternative would probably be to put this in your repo root
and add it to [global gitignore](https://sebastiandedeyne.com/setting-up-a-global-gitignore-file/).
So now with worktrees, rather than checkout a new branch everytime I want to do
something, all I have to do is to cd into the worktree directory with the
checked out branch and do whatever I need.

I won't go into the details but here's a concise [video
tutorial](https://www.youtube.com/watch?v=4_p1OdLeDLE) that explains how it
works quite well. Anyways that's all I wanted to share.

