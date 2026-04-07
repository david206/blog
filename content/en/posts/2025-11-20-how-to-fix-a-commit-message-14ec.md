---
title: "How to Fix a Commit Message"
draft: false
devto_id: 3035303
slug: "how-to-fix-a-commit-message-14ec"
cover_image: "/devto-assets/how-to-fix-a-commit-message-14ec/cover-1.png"
devto_url: "https://dev.to/david_shimon/how-to-fix-a-commit-message-14ec"
devto_published: false
date: "2025-11-20T07:02:51Z"
description: "Good commit messages are important for improving collaboration, speeding up debugging, documenting a..."
tags:
  - "beginners"
  - "git"
  - "tutorial"
---

Good commit messages are important for improving collaboration, speeding up debugging, documenting a project's history, and simplifying code reviews.

But sometimes we make typos in our commit messages because we are, for now, human. Sometimes the message we wrote isn't clear or detailed enough. In any case, sometimes we want to change the message we wrote for a commit.

In this post, we'll go over two simple ways to change a commit message to make it clearer, more organized, more accurate, correct, etc.

**For example, you might want to change:**
```bash
# Vague message
fix bug

# To something more descriptive
Resolve null pointer exception in user login validation
```

## Case 1: Fixing the Last Commit Message

We want to fix the message of the last commit we made. The command `git commit --amend` allows us to change the last commit. 

Make sure there are no changes you've added to staging with `git add`, because we only want to change the message, not the files in the commit (I'll cover changing commit content in a future post). 

After running `git commit --amend`, an editor will open and allow us to change the message. Edit, save, [exit](https://stackoverflow.com/questions/11828270/how-do-i-exit-vim) - and we're done.

## Case 2: Fixing an Older Commit Message

What happens if we want to change the message of an older commit? One we made four commits ago, for example, and now we've decided we want to change it?

The command we'll use this time is interactive rebase:
```bash
git rebase -i <base>
```

Where `base` is a commit in the branch history that comes before the commit we want to change. For example, to modify the last three commits, you would use `HEAD~3`. If all your branch commits are after the main branch, use `git rebase -i main`.

An editor will open showing all the commits we've made since the base commit, and will allow us to make changes to their history. Before each commit there's a command that tells Git what we want to do with this commit during the rebase.

```bash
pick 4698cbda # Change post header
pick 2f95451c # Add cover image
pick fdae7c1b # Comit message with typo we want to fix

# Rebase 517de4f8..fdae7c1b onto 517de4f8 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
```

The command we're interested in for this post is `reword` - which says: use the commit but edit its message. We'll find the commit (or commits) we want to change, and replace the default `pick` command (which means use the commit as is) that appears before it with `reword`, or just the letter `r` for short.

When we save and exit the editor, the rebase will start executing the commands, and will open an editor with the previous message of the commit that we can change, save and exit until the rebase completes successfully.

## ⚠️ Warning: You're Rewriting History

In both cases, we're changing history. Even though it's seemingly just a message change, it actually creates a new commit. Therefore, don't do this on branches that are shared with other people and not with commits that have already been merged into main, because it will create a mess for the team. Do this on a feature branch you're working on alone, and that hasn't been merged yet.

If you've already pushed the branch to origin, you won't be able to push it again simply, because you've changed the commits that were there. To push the updated commits, you'll need to use `git push --force-with-lease`. We're essentially saying that our local, corrected changes are what we want and we're happy to give up what's in origin.

## Conclusion

Now your reviewer will be able to understand what you meant in each commit, and if in the future you need to understand why you made the change - you'll see clear and well-written messages on the commit.
