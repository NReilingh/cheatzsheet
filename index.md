---
layout: default
title: A Zsh Cheat Sheet
---

# CheatZsheet

This is a distillation of sorts of the [Zsh User Guide](https://zsh.sourceforge.io/Guide/).
This assumes you understand the basics of how to use a shell --
entering commands, recalling history, a pipeline here and there.
This aims to provide a series of tips for leveling up,
illustrating the concepts, shortcuts, and commands that are most useful
or most frequently issued, without going too deep into specifics.

Also, this is written from the perspective of a macOS user who wants to
keep their shell fairly standard instead of developing muscle memory
for something completely custom.
Portability of vocabulary between zsh and bash is also prioritized.

### Notation

I will use typical symbolic shorthand shown by `bindkey`, like `^C` for control-c.

## Startup Files

Different sets of startup files are run based on whether the shell
is interactive and/or is a login shell.

A login shell is the first shell started for a user when they are allocated a tty.
Any subshells created in that tty session, whether or not they are interactive,
are never login shells (unless started with options to emulate this).

Zsh's startup files appear in a `/etc/*` variant and a `~/.*` variant.
Files are run in the following order, and always execute the `/etc/*` variant first.

1. `zshenv` - All zsh instances
2. `zprofile` - Login shells (before zshrc)
3. `zshrc` - Interactive shells
4. `zlogin` - Login shells (after zshrc)

Environment variables (`export`-ed) should thus ONLY be included in `zprofile`
or `zlogin`.
Remember, since these are only run for login shells,
then under normal circumstances you ensure those variables are
only exported ONCE per tty session.
Exporting envionment variables in `zshrc` or `zshenv` (despite its name)
would cause them to be re-exported every time you create a sub-shell,
which is particularly messy if you are appending something to the $PATH,
for example.

So: `zshenv` is for settings or options that are global to every zsh instance,
`zshrc` is for keyboard shortcuts, tab completion, prompt customization,
and other things specific to interactive shells,
and `zprofile` or `zlogin` is for exporting environment variables or anything else
that is session-specific.

Now let's contradict all of that: some path components or environment variables
you will want available in shells that do _not_ descend from the login shell,
like those created by cron/launchd jobs or spawned by GUI applications.
In those cases, then `zshenv` is your only option,
but luckily zsh has some special tools for dealing with PATH:
the $path variable is used instead as an array mirror of what's in $PATH.
So you can do this instead:

```
typeset -U path
path=(~/bin ~/progs/bin $path)
```

Exported environment variables can simply be set and re-set to the same value
without issue.

## Useful Commands

`stty -s` shows you what shortcuts your TTY understands.

`bindkey` shows you what shortcuts the shell understands (specifically the zsh line editor, zle).

`cd thing1 thing2` does a find/replace for thing1 in the current path with thing2 and `cd`s to it.

`pushd`, `popd`, `dirs` can be used to manipulate a directory stack instead of `cd` which is limited to `cd -`.

Use `getopts`: https://zsh.sourceforge.io/Guide/zshguide03.html#l44

Custom functions using `autoload` and defined in an `$fpath` directory: https://zsh.sourceforge.io/Guide/zshguide03.html#l48

`emulate -R zsh` Sane options reset at the top of a script or function

## Z-Shell Line Editor

`^A` "home"

`^E` "end"

`^B`/`^F` back/forward -- cursor movement in line

`^N`/`^P` next/previous -- history

These next few are a sequence -- `^[` is either escape or control-left square bracket, same as vim.

`^[B`/`^[F` back/forward by word

`^[Q` push the current line on to an edit buffer stack and clear for a different command.
Good if you started typing something complicated but need to do something else first.

`^[<Return>` Instead of executing the current line, add a line break and behave like you were writing a script.