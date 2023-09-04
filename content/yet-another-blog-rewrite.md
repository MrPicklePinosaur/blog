+++
title = "Yet another blog rewrite"
date = 2023-02-26

+++

```rust
fn main() {
    println!("hello world");
}
```

So somehow I have managed to rewrite my blog *once again*. This time we are using
[zola](https://getzola.org), which is a static site generator written in rust.
It's pretty nice since you can do everything in markdown and it comes with a
bunch of features like search index, syntax highlighting etc. I have finally
caved in and decided to use modern technologies for once instead of the
insanity of what I was working with before.

First blog was a very long time ago when I wanted to learn django. This was
unfinished so it doesn't really count.

Next is this [shell script](https://github.com/MrPicklePinosaur/pb)
that tacks on new blog posts to an existing file. It worked okay but got
annoying when I wanted to change up the format of the blog pages or wanted to
edit stuff. It was a reasonable project since I was learning shell scripting.
I actually made a decent couple posts using this blog system.

After this was when I became mentally ill. Here is a makefile based [blog
script](https://github.com/MrPicklePinosaur/pinopress) that is more or less
finished. It started off quite elegant with me using `envsubst` to do variable
substitutions in md files as templating. It then spiraled into me trying to
write a templating engine in makefile complete with a md to html generator
written in sed. Honestly if I polished up this project and finished the sed
script it could be a pretty decent and lightweight static blog generator.

Needless to say, I got a bit tired of writing shell scripts for my blogs and
just wanted something that works and has all the features I wanted. So that's
why I'm using a prebuild tool for once. Perhaps if I get bored I'll write
another blog script but we shall see :yum:.

