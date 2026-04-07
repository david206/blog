---
title: "How AI Agents Changed What I Write About (And Why You Should Still Care About Git)"
draft: false
devto_id: 3214513
slug: "how-ai-agents-changed-what-i-write-about-and-why-you-should-still-care-about-git-5g72"
cover_image: "/devto-assets/how-ai-agents-changed-what-i-write-about-and-why-you-should-still-care-about-git-5g72/cover-1.png"
devto_url: "https://dev.to/david_shimon/how-ai-agents-changed-what-i-write-about-and-why-you-should-still-care-about-git-5g72"
devto_published: false
date: "2026-02-02T11:29:33Z"
description: "The Moment Everything Changed   Last week, I was working with Claude Code on a feature. We..."
tags:
  - "agents"
  - "ai"
  - "git"
  - "softwareengineering"
---

## **The Moment Everything Changed**

Last week, I was working with Claude Code on a feature. We were in flow \- the kind where you're solving problems faster than you can type. The code was good, the tests passed, and we had a commit ready to go.

Then I looked at the commit message.

It was organized, clear, and listed three distinct changes we'd made.

Three separate things. In one commit.

I paused. I'd just written a blog post about atomic commits. About how each commit should tell one story. About how good git history makes code reviews easier and future debugging possible.

And here I was, about to commit the exact kind of change I'd been preaching against.

So I asked Claude Code: "Can you split this into three atomic commits?"

And it did. Perfectly. Exactly how I would have done it manually.

That's when the doubt crept in.

## **"So Why Did I Even Write This Post?"**

For the first time in 20 years as a developer, I'd been writing technical blog posts. The rise of LLMs gave me the confidence to write in English. I found myself explaining things I knew well, and that's how I discovered good topics.

Three of my posts were about git. About how to structure commits so they have value. So they help whoever reviews the changes and whoever reads the code in the future.

But now, staring at Claude Code's perfectly split commits, I felt my motivation drain away.

Why does it matter that I know all the tools, shortcuts, and tricks, in a world where I can just ask the agent to do the work?

There are already companies where nobody writes code anymore. People are shipping to production without even looking at what was generated.

What's the value in writing posts that don't directly address how to work with LLMs?

## **The Answer Was Staring Me in the Face**

Then it hit me.

Claude Code split those commits perfectly **because I asked it to**.

It didn't look at that three-part commit message and think "hmm, this should probably be three commits." It would have happily pushed that large commit if I hadn't intervened.

I was the one who recognized there was a problem. I was the one who knew what good commits look like. I was the one who could evaluate whether the split was done well.

The agent didn't replace my knowledge.It amplified it.

All those posts I'd written about git? That's what gave me the ability to spot the issue and know how to fix it, even through a tool.

## **From Developer Instructions to Agent Instructions**

After working with Claude Code a few more times, I did something interesting: I created a skill that encodes the principles of atomic commits directly. Now, every time the agent creates a commit, it automatically:

* Asks itself whether it's atomic  
* Ensures it has a clear message  
* Splits it if needed

But here's the thing: **I couldn't have created that skill without understanding the principles**.

The skill didn't invent the rules. It encodes what I already knew, what I wrote about in my post on [The Madman, Architect, Carpenter, and Judge](https://dev.to/david_shimon/the-madman-architect-carpenter-and-judge-turning-git-commits-into-stories-19b3).

This is exactly what I mean when I say knowledge has shifted from "instructions for developers" to "instructions for agents." Same principles, same importance. Only the execution has changed.

And even with the skill in place, I still need to check. The agent isn't perfect. Sometimes I need to correct or redirect it. **That requires the same understanding** that would have been needed to write the code myself. I'm just using it for review and direction instead of manual implementation.

## **The Calculator Analogy**

We all have calculators in our pockets. But we still learn multiplication tables by heart. We still learn long division with paper and pencil. We understand the calculations.

Not because we want to be faster than the calculator.

We learn it because:

* It helps us understand what the calculator is doing  
* It gives us intuition for when a result looks wrong  
* It lets us estimate results mentally for simple cases  
* It helps us know the meaning of the calculation, and when we should use it  
* It's the foundation for understanding more advanced concepts

The same is true for `git add --patch`, or any other technical skill. You don't need to be faster than Claude Code. But you should know what it's doing under the hood.

## **Two Futures**

There's both opportunity and risk here.

**The Opportunity:** If you understand the principles, AI agents make you super-productive. You can ask "split this into atomic commits" and get a perfect result. You can focus on the *why* and the *what*, while the AI handles the *how*.

**The Risk:** If you don't understand the principles, you'll be dependent on the AI's defaults. You'll get large commits because you didn't know to ask for something different. You'll get code that works but isn't readable. You'll ship to production without looking, and discover problems only when they explode.

**The Reality:** To use these tools well, you need to understand what's happening under the hood. To know **what to ask for**, **when the result doesn't look right**, and **how to fix it**.

## **What This Means for Writing**

This changes what technical blog posts should be.

No more guides on "how to run `git rebase -i` step by step." Instead, posts about:

* **Why** atomic commits matter (not how to create them)  
* **What** makes code readable vs just working (not how to write it)

And yes, still some “**how”**. A peek under the hood. Not a tutorial, but enough understanding so you know what's happening. Like learning long division even though you have a calculator.

Because in a world where AI writes the code, our job is to know what to ask for, why it matters, and how to check that we got what we wanted.

In the past, this knowledge was implicit in our technical skills. Now it needs to be explicit.

And that's exactly what this blog will do going forward: show you what's possible, why it matters, and enough "how" so you understand what's happening.

Then you can make sure it happens for you too \- whether you do it yourself, or guide an AI agent to do it for you.
