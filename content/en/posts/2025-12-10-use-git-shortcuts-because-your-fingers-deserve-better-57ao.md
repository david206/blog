---
title: "Use Git Shortcuts - Because Your Fingers Deserve Better"
draft: false
devto_id: 3096967
slug: "use-git-shortcuts-because-your-fingers-deserve-better-57ao"
cover_image: "/devto-assets/use-git-shortcuts-because-your-fingers-deserve-better-57ao/cover-1.png"
devto_url: "https://dev.to/david_shimon/use-git-shortcuts-because-your-fingers-deserve-better-57ao"
devto_published: false
date: "2025-12-10T17:09:20Z"
description: "If you're running git checkout -b feature/new-thing dozens of times a day, you're wasting precious..."
tags:
  - "git"
  - "productivity"
  - "zsh"
  - "terminal"
---

If you're running `git checkout -b feature/new-thing` dozens of times a day, you're wasting precious seconds and mental energy. There's a better way.

## Why Command Line?

I work with git from the command line. Not (only) because I'm old fashioned, but because once you know the shortcuts, it's faster than reaching for your mouse. My IDE has a great integrated terminal, and with the right aliases, I can navigate git in seconds.

## The Secret Weapon: Oh My Zsh Git Plugin

I didn't invent most of the shortcuts I use. I use them from the git plugin of Oh My Zsh (a popular framework for managing your Zsh configuration), which provides a huge collection of aliases and functions that make working with git much faster.

My team members also installed the plugin. Now we speak the same language - when I say "just `gpsup` it," they know exactly what I mean. We can share tips, and when pair programming, the muscle memory transfers seamlessly between keyboards.

> "But I'll Never Remember These Cryptic Shortcuts!"

I hear you. `gpsup` looks like alphabet soup at first. But here's the thing: they're surprisingly logical once you see the pattern. Most shortcuts follow a simple formula: g (git) + first letters of the command. So gcb = git checkout -b, gco = git checkout, gcmsg = git commit -m (msg). Your brain picks up the pattern faster than you'd think.

## How to Enable the Plugin

The git plugin comes bundled with Oh My Zsh, but you need to enable it in your `.zshrc`:
```bash
plugins=(git)
```

## Start Simple: The Essential Shortcuts

Don't try to learn everything at once. Start with the commands you use most often. Like the legend about Harvard's paths that were paved in the ways students walked the most. Here's how to find them:

```bash
# See your most-used git commands
history | grep git | awk '{print $2, $3}' | sort | uniq -c | sort -rn | head -10
```


Check what you use most often and find a shortcut for that command, and get used to using it. 

For most developers, these basics cover 80% of daily work:
- `gst` - `git status`
- `gco` - `git checkout`
- `gcb` - `git checkout -b` create new branch
- `gp` - `git push`
- `gl` - `git pull`
- `ga` - `git add`
- `gcmsg` - `git commit -m`
- `glog` - beautiful git log with graph

*Pro tip:* Wondering what a shortcut actually does? Type `which <shortcut>` in your terminal to see the full command.

Once you get used to these shortcuts, they become muscle memory. You stop thinking about git syntax and stay focused on your code. That's the real win - maintaining flow instead of context-switching to remember command flags.

Commands I mentioned in my previous posts about git:
- `gapa` - `git add --patch` interactively choose which changes to stage from each file
- `grbi` - `git rebase -i` interactive rebase
- `gc!` - `git commit -v --amend`. Forgot to include a file in your last commit? Made a typo in the commit message? This fix your last commit without creating a new one. No more "oops, fix typo" commits cluttering your history

## My Favorite Power Shortcuts

- `gpsup` - No more copying error messages when pushing a branch for the first time. This shortcut runs `git push --set-upstream origin $(current_branch)` automatically. One command, done.

- `gwip` and `gunwip` - Perfect for when you need to quickly switch contexts without losing work. `gwip` creates a Work In Progress commit with all your changes, and `gunwip` removes it when you're ready to make a proper commit. Great for handling urgent bugs mid-feature.

- `gbda` - Clean up 10 merged branches in one command instead of typing `git branch -d` ten times. After a sprint with multiple feature branches merged, this is a lifesaver.

- `gsta` and `gstp` - Stash shortcuts for when you need to quickly save work and come back to it. Much faster than typing out `git stash` and `git stash pop`.

## A Real Workflow Example

Here's my typical feature branch workflow:
```bash
gcb new-feature          # Create and switch to new branch
# ... do some work ...
gapa                     # Interactively stage changes
gcmsg "Add new feature"  # Commit with message
gpsup                    # Push and set upstream
```

Compare that to typing it all out manually:
```bash
git checkout -b new-feature
# ... do some work ...
git add --patch
git commit -m "Add new feature"
git push --set-upstream origin new-feature
```

The shortcuts aren't just faster - they let you stay in the zone instead of wrestling with git syntax.

## Learning Hidden Git Gems

A bonus of using the plugin: you'll discover git commands and flags you didn't know about. The plugin authors have made thoughtful choices about safer alternatives and useful flags.

For example, `gpf` isn't just `git push --force` - it's actually `git push --force-with-lease` (or `git push --force-with-lease --force-if-includes` on newer Git versions), which is a safer alternative that prevents you from accidentally overwriting someone else's work. If you really need the dangerous `--force`, that's `gpf!` with an exclamation mark.

Another example: even the basic `gc` (commit) uses the `-v` (verbose) flag. It runs `git commit -v`, which shows you the actual diff of your changes at the bottom of the commit message editor. You can review what you're committing while writing the message - a small detail that makes a big difference in writing accurate commit messages.

If you want to learn about other useful flags on git commands, e.g. “git log”, try `alias | grep "git log"`, read about them, try them, and adopt what you like. Maybe `glols` is your preferred way to see your git log? It's like git log but prettier and includes human readable relative time and which files were changed per commit.


## Adding Your Own Custom Aliases

While the Oh My Zsh git plugin is incredibly comprehensive, you might find yourself wanting shortcuts for workflows it doesn't cover. Here's one I added that's become essential to my workflow:

### The `fixup` Alias

- `fixup` - `git commit --fixup`

Add this to your `~/.oh-my-zsh/custom/aliases.zsh` file:

```alias fixup='git commit --fixup'```

This is a game-changer for cleaning up commits during code review. When reviewers point out issues in specific commits, instead of creating "fix typo" or "address review comments" commits, I use `fixup` to mark them for automatic squashing during an interactive rebase with `--autosquash`. It keeps your commit history clean without the manual work of reordering commits.

## Common Questions

> "What if I'm on a machine without my aliases?"

This is real pain. My solution: keep the most critical commands in muscle memory both ways. I can still type git status when needed, but gst is my default.

> "How do I see all available aliases?"

Run `alias | grep git` to see everything. It's a long list! Or check the [official plugin documentation](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/git).

> "Can I customize existing aliases?"

Yes! Add your overrides to `~/.oh-my-zsh/custom/aliases.zsh`. Your custom aliases take precedence over plugin defaults.

> "Should I learn the full commands first?"

Honestly? Yes. Understanding what `git rebase -i` does makes `grbi` powerful instead of magical. The shortcuts are a speed layer on top of solid fundamentals.

## The Bottom Line

Git shortcuts aren't just about saving keystrokes - they're about staying in flow, reducing context switching, and making git feel less like a chore.

Give it a try. Your future self (and your fingers) will thank you.

---

*What are your favorite git shortcuts? Share them in the comments!*
