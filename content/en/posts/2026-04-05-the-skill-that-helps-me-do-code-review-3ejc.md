---
title: "The Skill That Helps Me Do Code Review"
draft: false
translationKey: "the-skill-that-helps-me-do-code-review"
devto_id: 3455907
slug: "the-skill-that-helps-me-do-code-review-3ejc"
cover_image: "/devto-assets/the-skill-that-helps-me-do-code-review-3ejc/cover-1.png"
devto_url: "https://dev.to/david_shimon/the-skill-that-helps-me-do-code-review-3ejc"
devto_published: false
date: "2026-04-05T08:41:07Z"
description: "I was complaining. Again.  Code review was eating my time, and the problem was only getting worse. We..."
tags:
  - "ai"
  - "codereview"
  - "skills"
---

I was complaining. Again.

Code review was eating my time, and the problem was only getting worse. We were shipping more features faster, which meant more code, much of it in domains I don't know deeply. Agentic development is great for output. It's not great for the person who still has to *understand* what got built.

[Amitai](https://dev.to/amitaile), a teammate, got tired of hearing about it. He sent me a link.

---

## Tip 26

The article was *[45 Claude Code Tips: From Basics to Advanced](https://github.com/ykdojo/claude-code-tips?tab=readme-ov-file#tip-26-interactive-pr-reviews)* by [@ykdojo](https://x.com/ykdojo). Tip 26 was about interactive PR reviews:

> Claude Code is great for PR reviews. The procedure is pretty simple: you ask it to retrieve PR information using the gh command, and then you can go through the review however you want.
>
> You can do a general review, or go file by file, step by step. You control the pace. You control how much detail you want to look into and the level of complexity you want to work at. Maybe you just want to understand the general structure, or maybe you want to have it run tests too.
>
> The key difference is that Claude Code acts as an interactive PR reviewer, not just a one-shot machine. Some AI tools are good at one-shot reviews (including the latest GPT models), but with Claude Code you can have a conversation.

I tried it once. Then again. Then I turned it into a skill.

---

## Why I still read the code

Before getting into the workflow, I want to address the elephant in the room.

Code review in the agentic era is genuinely contested. Some people think it's becoming obsolete. I don't.

Agents write a lot of code. They also produce a lot of slop: suboptimal solutions, non-standard patterns, things that don't match your team's conventions, and sometimes just plain bugs. The responsibility is still ours. Even Boris Cherny, the creator of Claude Code, says he still reads the code he generates.

There's also a practical reason: the person who wrote the code might be on vacation next week. I need to be able to own it, change it, and defend it. That only happens if I actually understood it during review.

At PhaseV, CoPilot runs automatic review on every PR. On top of that, I manually run one-shot reviews using Codex and Claude Code's built-in skills. They catch real things. But automated review isn't the same as understanding the code. That still requires a human.

---

## How the flow works

Our team writes commits that tell the story of a change. Each commit is atomic and focused.

I type `/review-pr-interactive` and the PR number. From there:

- Claude checks out the branch and summarizes the PR's goal
- It lists every commit with a one-line description
- It starts on the first commit: explains what's happening, flags what deserves attention, and asks for my take *before* we move on
- I read the commit myself. If it's larger than expected, I ask Claude for a recommended reading order for the files before diving in
- We decide together what's worth commenting on
- Claude uses the GitHub CLI to post the comment on the exact relevant line

Then we move to the next commit.

When I hit something I don't recognize, I ask Claude and learn something new, then usually post it on [Twitter](https://x.com/davidshimon) under `#TIL`.

---

## Why a skill and not just a prompt

At this point you might ask: why formalize this as a skill? You could just tell Claude what you want each time.

The answer is consistency. Every time I start a review, Claude already knows our team's conventions, the tone we use for comments, what counts as a Nitpick, and that it should wait for my approval before posting anything. I don't re-explain. I don't forget to mention something. The skill is the accumulated knowledge of every session that didn't go quite right.

One concrete example: before I wrote the skill, it took Claude around 3 attempts each session to figure out the right `gh` command to add a comment on a specific line in a PR. Now it's written into the skill, and it gets it right the first time.

It also lowers the activation energy. Typing `/review-pr-interactive 847` is frictionless. Writing a detailed prompt from scratch is not.

---

## What we added along the way

After using it daily, we refined the skill to fit how our team works:

- **Define the focus** — set what matters most upfront for this type of PR
- **Nitpick labeling** — it's fine to flag style issues, but mark them clearly as Nitpicks
- **Boy Scout commits** — we allow unrelated cleanup in a separate commit within the PR. No separate PR required, just keep it isolated
- **No auto-commenting** — Claude waits for my explicit approval before posting anything

A separate thing we had to shape: how Claude behaves when approving a PR. Early on it would write a full essay summarizing everything the PR did as part of the approval message. Very strange. The PR creator knows what they built. We defined in the skill that a simple LGTM is enough, or a brief note on anything that genuinely stood out.

---

## The thing that surprised me

I expected this to save time. It did.

What I didn't expect was that it would make me *more* present in the review, not less. With one-shot automated tools, I read a list of findings and move on. With this flow, I'm actually in the code: reading, asking questions, making judgment calls. The AI handles the scaffolding. I bring the understanding.

That feels like the right division of labor.

---

*How are you handling code review as AI writes more of your codebase?*
