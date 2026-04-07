---
title: "The Madman, Architect, Carpenter, and Judge: Turning Git Commits Into Stories"
draft: false
devto_id: 3089403
slug: "the-madman-architect-carpenter-and-judge-turning-git-commits-into-stories-19b3"
cover_image: "/devto-assets/the-madman-architect-carpenter-and-judge-turning-git-commits-into-stories-19b3/cover-1.png"
devto_url: "https://dev.to/david_shimon/the-madman-architect-carpenter-and-judge-turning-git-commits-into-stories-19b3"
devto_published: false
date: "2025-12-06T18:53:32Z"
description: "We've all done it: make a bunch of changes, then commit everything with a vague message like \u201cfix..."
tags:
  - "git"
  - "commit"
---

We've all done it: make a bunch of changes, then commit everything with a vague message like “fix issues”. But what if we approached Git commits like a writer approaches a manuscript?

When I work on a large change, my commits aren't "pretty" on the first attempt. Still, I invest the time to organize them. I break the changes into atomic pieces \- each one doing a single thing that's very easy to understand and review. For example: in the first commit, add a new type of constraint; in the second commit, add a negative constraint; in the third commit, modify one message to use both new constraints; and in the fourth commit, update another message that now needs to use the new constraints.

## **The Four Roles of Writing**

Betty S. Flowers, Emerita Professor of English at the University of Texas at Austin, [wrote](https://www.ut-ie.com/b/b_flowers.html) about roles in the writing process, offering a solution to writer's block by separating the creative and critical aspects of writing. She identified four roles that participate in the writing process, and to avoid getting stuck, they shouldn't "talk" to each other while we're writing. Instead, we need to "wear" a different hat at each stage so each can work without interference. The stages she identifies are: the Madman, the Architect, the Carpenter, and the Judge. If you want to write, I recommend reading her article to understand what each role does at each stage of writing.

## **Applying This to Git Commits**

I borrow her idea from prose writing:

**Stage One \- The Madman** writes the code without thinking too much about who will read it. Fixes what needs fixing, adds what needs adding, checks that everything works and does what it should, without too much self-control.

**Stage Two \- The Architect** comes to organize all the code the Madman wrote. They find the logic in the madness, identify the different stages, separate into the different parts that create the commits that tell the story of the change.

**Stage Three \- The Carpenter** organizes each individual commit \- ensures it contains exactly what it should, chooses an appropriate title, and details in the comment about the essence of the change.

**Stage Four \- The Judge** examines the finished work \- plays the role of the reviewer.

![The Four Roles of Clean Git Commits](/devto-assets/the-madman-architect-carpenter-and-judge-turning-git-commits-into-stories-19b3/inline-1.png)


## **Why Is This Worth the Investment?**

This is essentially self code review. As a code reviewer, it's much easier to review a pull request that's divided into clear, well-defined, and short commits. But beyond hoping I'm making my reviewers' lives easier, the main value of my work is that I'm doing a review for myself. I need to explain the change to myself \- why I did each thing and how \- in order to build the pull request this way. The additional pass over the recently written code, this time with different eyes \- looking for the different stages in the change, the order and logic \- helps me improve my code.

### The Concrete Benefits

**Faster, better code reviews**: Reviewers can understand and approve changes faster when they can review one logical change at a time. Instead of facing a wall of changes, they can follow your thought process step by step.

**Easier debugging**: When something breaks, `git bisect` becomes a powerful tool. With atomic commits, you can quickly pinpoint which specific change introduced a bug. Compare this to commits that bundle multiple unrelated changes \- bisect becomes nearly useless.

**Safer reverts**: If you need to roll back a change, atomic commits let you revert precisely what's broken without losing good work. You can revert just the third commit while keeping the first two, rather than throwing away an entire day's work.

**Better documentation**: Six months from now, when you (or a teammate) run `git blame` to understand why something works the way it does, a well-crafted commit with a clear message tells the story. The commit history becomes living documentation that explains not just *what* changed, but *why*. It's like having a time machine. I can go back and ask the writer (usually my past self) why this change was done, and get a clear answer instead of a shrug.

![Time machine to go back and ask the writer why this change was done](/devto-assets/the-madman-architect-carpenter-and-judge-turning-git-commits-into-stories-19b3/inline-2.png)


**Smoother onboarding**: New team members can read through your pull requests and commit history to understand how changes are structured and how features are built incrementally. It's like leaving breadcrumbs for future developers.

The time invested in organizing commits pays dividends every time someone (including future you) needs to understand, debug, or modify the code.

## **The Tools of the Architect and Carpenter**

Once you embrace this workflow, you'll need the right tools to reshape your commits. Here are the essential commands:

### **Interactive Rebase: Your Primary Tool**

Interactive rebase is the Swiss Army knife for organizing commits. It allows you to rewrite history before pushing:  
`git rebase -i HEAD~5
`
This opens an editor showing your last 5 commits, where you can:

* **Reorder commits** \- move them up or down to tell a better story  
* **Edit commits** \- modify the code or message of a specific commit  
* **Squash commits** \- combine multiple commits into one  
* **Split commits** \- break one large commit into smaller atomic pieces  
* **Drop commits** \- remove commits that aren't needed

Each commit in the editor is prefixed with a command (`pick`, `reword`, `edit`, `squash`, etc.). Change the command, save the file, and Git walks you through the changes.

### **Stage Partial Changes**

Sometimes the Madman mixed multiple logical changes in a single file. The Carpenter can separate them:

`git add -p
`

This command presents each change in the file and asks: "Stage this hunk?" You can answer `y` (yes), `n` (no), `s` (split into smaller pieces), or `e` (manually edit). This lets you commit only the relevant parts of a file, keeping each commit focused.

**Example workflow**: You fixed a bug and also renamed a variable in the same file. Use `git add -p` to stage only the bug fix for one commit, then stage the rename for a separate commit.

### **Quick Fixes with \--fixup**

When the Judge or the real reviewer find issues during review, create fixup commits:

`git commit --fixup <commit-hash>
`

This creates a commit labeled `fixup! Original commit message`. Later, when you run `git rebase -i --autosquash`, Git automatically merges these fixup commits into their targets. It's perfect when you're polishing individual commits.

**Pro tip**: Make autosquash the default behavior so you don't have to remember the flag:

`git config --global rebase.autosquash true
`

## **Conclusion**

Clean commits aren't just about being a good team player—they're about being kind to your future self. The time you invest in organizing your commits today pays back every time you need to debug an issue, understand why a decision was made, or safely revert a change.

Start small: try this approach on your next pull request. Put on the Madman's hat and write your code freely. Then become the Architect and find the story in your changes. Let the Carpenter polish each commit. Finally, be the Judge and review your own work.

Your reviewers will thank you. Your future self will thank you. And you might just write better code along the way.
