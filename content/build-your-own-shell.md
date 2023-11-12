+++
title = "Build your own shell in Rust with shrs"
date = 2023-11-11
draft = true
+++

In classic shells we are usually limited by what the developers of that shell
has deemed within reason. Us end users are only allowed configuration through a
set of options, and perhaps some scripting abilitiy through the shell language.
For most users, this is enough flexibility to get them through their day. But
what if we wanted more? What if I really loved fish shell's awesome tab
completion, but liked the comfort of bash? [BETTER EXAMPLES] What if I wanted
some completely new feature that would be almost impossible to implement
without numerous hacks? [ADD ACEDOTE ABOUT HARD TO IMPLEMENT FEATURE]. Well,
what if we had a shell where the source code was the configuration file?

In fact, this idea isn't that far fetched. The
[suckless](https://suckless.org/) group has been doing this for ages with their
wonderfully minimalist software like [dwm](https://dwm.suckless.org/) (window manager) and
[st](https://st.suckless.org/) (terminal). Instead of the developers exposing a
friendly set of knobs and dials for the user, they gave them the entire machine
to toy and tinker with. ... maybe mention xmonad and penrose

So what shrs aims to do is provide [FINISH]



With that said, let's get started with creating our very own shell!

At the end of the day, shrs is a library, not a program. So we are first going
to make a new cargo project and pull shrs as a dependency. Let's call it
`aweshell` for 'awesome shell' or something:
```sh
cargo new aweshell
```

Next we add shrs as a dependency in the `Cargo.toml`. Note that since shrs is
still in pre-release, we will be getting frequent updates with potentially
breaking changes. You can use the latest release by using version 0, or use a
more specific version if you don't wish to pull breaking changes right away:
```toml
shrs = { version = "0" }
```

And with that we are ready to get coding. The most basic shrs shell only needs
the following code in `src/main.rs`:
```rust
use shrs::prelude::*;

fn main() {
    let aweshell = ShellBuilder::default()
        .build()
        .unwrap();

    aweshell.run();
}
```

Now compile and run with:
```sh
cargo run
```

And you should see shrs running with the bare minimal features and no configuration.
```
welcome to shrs!
>
```

Don't be deceived by the simplicity, this is where the fun starts. How about we
first change the start message to something flashier? The start message is
rendered using a **hook** - an event that we can listen for and run our own
code on. In particular, the hook we are interested in is the `StartupHook`. A
hook is just a function that shrs will call for us. Don't be intimidated by the
first three arguments, these just give us access to the shell internals for us
to do other fun things, we will talk about this later. Do however pay attention
to the `ctx` argument. This is data passed specifically by the hook that is
called and `StartupCtx` in particular will give you information like how long
it took the shell to initialize. We don't need any of this for now, we will
just print a nice message.

```rust
use shrs::anyhow;
fn my_startup_hook(sh: &Shell, ctx: &mut Context, rt: &mut Runtime, hook: &StartupCtx) -> anyhow::Result<()> {
    println!(r#"
                         _          _ _ 
  __ ___      _____  ___| |__   ___| | |
 / _` \ \ /\ / / _ \/ __| '_ \ / _ \ | |
| (_| |\ V  V /  __/\__ \ | | |  __/ | |
 \__,_| \_/\_/ \___||___/_| |_|\___|_|_|
                                        
    "#);
    Ok(())
}
```

Next we need to tell shrs to call this hook on the startup event, we can do so
by passing it in the initalization process:
```rust
fn main() {
    let mut hooks = Hooks::new();
    hooks.register(my_startup_hook);

    let aweshell = ShellBuilder::default()
        .with_hooks(hooks)
        .build()
        .unwrap();

    aweshell.run();
}
```

Now just like that, if we run our shell again we can see our hook being called.

